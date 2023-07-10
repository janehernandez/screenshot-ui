<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md" style="width:95%;">
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md">
        <q-input v-show="showAuth" filled v-model="auth" label="Authentication *" hint="Authentication info from OAI"
          lazy-rules :rules="[val => val && val.length > 0 || 'Please type something']" />
        <q-input filled v-model="url" label="Coaching URL *" hint="Coaching URL" lazy-rules />

        <div>
          <q-btn label="Submit" type="submit" color="primary" />
          <q-btn label="Reset" type="reset" color="primary" flat class="q-ml-sm" />
        </div>
      </q-form>
    </div>
  </q-page>
</template>

<script>
import { useQuasar } from 'quasar'
import { ref } from 'vue'
import { api } from 'boot/axios'

export default {
  setup() {
    const $q = useQuasar()
    const auth = ref(null)
    const url = ref(null)
    const showAuth = ref(true)

    if (localStorage.getItem('AUTH_INFO')) {
      showAuth.value = false
      auth.value = localStorage.getItem('AUTH_INFO')
      const info = JSON.parse(localStorage.getItem('AUTH_INFO'));
      const { expiry } = info
      const expiryDate = new Date(expiry.substring(0, 10));
      const currentDate = new Date();

      if (currentDate > expiryDate) {
        $q.notify({
          color: 'red-5',
          textColor: 'white',
          icon: 'warning',
          message: 'Authentication is expired kindly ask your immediate superior for the new authentication'
        })

        localStorage.removeItem('AUTH_INFO')
        auth.value = null
        showAuth.value = true
      }
    }

    return {
      auth,
      url,
      showAuth,

      async onSubmit() {
        if (!auth.value || !url.value) {
          $q.notify({
            color: 'red-5',
            textColor: 'white',
            icon: 'warning',
            message: 'Please fill up the form'
          })

          return
        }

        $q.loading.show({
          delay: 400 // ms
        })

        localStorage.setItem('AUTH_INFO', auth.value)

        await api.get(`/screenshot?auth_info=${auth.value}&url=${url.value}`)
          .then(async (response) => {
            const { path, name } = response.data
            try {
              const link = document.createElement('a');
              link.href = path;
              link.download = name;
              link.target = '_blank'; // Optional: Set target to open in a new tab
              link.style.display = 'none';

              document.body.appendChild(link);
              link.click();
              document.body.removeChild(link);

              $q.loading.hide()
              url.value = null

              $q.notify({
                color: 'green-4',
                position: 'top',
                textColor: 'white',
                icon: 'cloud_done',
                message: 'Generation and downloading is success'
              })
            } catch (error) {
              console.error('Failed to download image:', error);
              $q.loading.hide()
              $q.notify({
                color: 'negative',
                position: 'top',
                message: 'Failed to download image',
                icon: 'report_problem'
              })
            }

          })
          .catch((err) => {
            $q.loading.hide()
            console.log(err)
            $q.notify({
              color: 'negative',
              position: 'top',
              message: 'Something went wrong',
              icon: 'report_problem'
            })
          })
      },

      onReset() {
        url.value = null
      }
    }
  }
}
</script>