<template>
  <q-page class="flex flex-center">
    <div class="q-pa-md" style="width: 95%">
      <q-form @submit="onSubmit" @reset="onReset" class="q-gutter-md q-mb-lg">
        <q-file
          filled
          bottom-slots
          v-model="image"
          label="Raw Coaching Image"
          counter
          :filter="checkFileType"
          @rejected="onRejected"
        >
          <template v-slot:prepend>
            <q-icon name="cloud_upload" @click.stop />
          </template>
          <template v-slot:append>
            <q-icon
              name="close"
              @click.stop="image = null"
              class="cursor-pointer"
            />
          </template>
        </q-file>

        <div>
          <q-btn label="Process Image" type="submit" color="positive" />
          <q-btn
            label="Reset"
            type="reset"
            color="primary"
            flat
            class="q-ml-sm"
          />
        </div>
      </q-form>

      <a
        v-if="downloadLink"
        class="btn-fixed-width q-mb-lg bg-accent q-pa-md q-my-lg text-white"
        :href="downloadLink"
        target="_blank"
      >
        <q-icon name="download" /> Click here to Download Processed Image
      </a>
    </div>
  </q-page>
</template>

<script>
import { useQuasar } from "quasar";
import { ref } from "vue";
import { api } from "boot/axios";

export default {
  setup() {
    const $q = useQuasar();
    const image = ref(null);
    const downloadLink = ref(null);

    return {
      downloadLink,
      image,
      checkFileType(image) {
        return image.filter((file) => file.type === "image/png");
      },

      async onSubmit() {
        if (!image.value) {
          $q.notify({
            color: "red-5",
            textColor: "white",
            icon: "warning",
            message: "Please add image",
          });

          return;
        }

        $q.loading.show({
          delay: 400, // ms
        });

        downloadLink.value = null;
        const formData = new FormData();
        formData.append("image", image.value);

        await api
          .post("/images/modify", formData, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          })
          .then(async (response) => {
            const { path, name } = response.data;
            downloadLink.value = path;
            try {
              const link = document.createElement("a");
              link.href = path;
              link.download = name;
              link.target = "_blank"; // Optional: Set target to open in a new tab
              link.style.display = "none";
              document.body.appendChild(link);
              link.click();
              document.body.removeChild(link);

              $q.loading.hide();
              $q.notify({
                color: "green-4",
                position: "top",
                textColor: "white",
                icon: "cloud_done",
                message: "Image processed successfully",
              });
            } catch (error) {
              console.error("Failed to download image:", error);
              $q.loading.hide();
              $q.notify({
                color: "negative",
                position: "top",
                message: "Failed to download image",
                icon: "report_problem",
              });
            }
          })
          .catch((err) => {
            $q.loading.hide();
            console.log(err);
            $q.notify({
              color: "negative",
              position: "top",
              message: "Something went wrong",
              icon: "report_problem",
            });
          });
      },

      onReset() {
        image.value = null;
      },

      onRejected(rejectedEntries) {
        // Notify plugin needs to be installed
        // https://quasar.dev/quasar-plugins/notify#Installation
        $q.notify({
          type: "negative",
          message: `${rejectedEntries.length} file(s) did not pass validation constraints`,
        });
      },
    };
  },
};
</script>
