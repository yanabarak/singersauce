<template>
  <div
    class="modal fade"
    id="imageModal1"
    tabindex="-1"
    aria-labelledby="imageModal1"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-dialog-centered modal-lg modal-1000">
      <div class="modal-content">
        <div class="modal-header position-relative border-0">
          <h2 class="modal-title fs-24 fw-bold text-center sec-font mx-auto" id="imageModal">
            Upload image
          </h2>
          <button
            type="button"
            class="btn-close position-absolute"
            data-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <div class="container-fluid">
            <p class="info text-center" v-if="!imageData">
              Click for selecting or drag and drop image
            </p>
            <vue-dropzone
              ref="myDropzone"
              id="dropzone"
              :options="dropzoneOptions"
              @vdropzone-file-added="fileAdded"
            ></vue-dropzone>
          </div>
        </div>
        <div class="modal-footer border-0 justify-content-center py-4 mb-2 gap-2">
          <button
            class="btn sec-btn px-5 col-3 justify-content-center"
            aria-label="Close"
            data-dismiss="modal"
            @click="cancel"
          >
            Cancel
          </button>
          <button
            class="btn main-btn px-5 col-3 justify-content-center"
            aria-label="Close"
            data-dismiss="modal"
            @click="saveImage"
          >
            Save
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueDropzone from 'vue2-dropzone';
import 'vue2-dropzone/dist/vue2Dropzone.min.css';

export default {
  components: {
    VueDropzone,
  },
  data() {
    return {
      imagePaste: '',
      imageSrc: this.imageData,
      width: 200,
      originalWidth: 0,
      originalHeight: 0,
      originalMouseX: 0,
      dropzoneOptions: {
        url: 'no-url', // No actual URL is needed since we don't perform an upload
        autoProcessQueue: false,
        maxFiles: 1,
        addRemoveLinks: true,
        acceptedFiles: 'image/*',
      },
      imageData: null,
      resizeWidth: 300, // Initial width
      resizeHeight: 300, // Initial height
    };
  },
  computed: {
    imageStyle() {
      return {
        width: this.width + 'px',
      };
    },
  },
  methods: {
    fileAdded(file) {
      const reader = new FileReader();
      reader.onload = e => {
        this.imageData = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    initResize(event) {
      this.originalWidth = this.width;
      this.originalHeight = this.height;
      this.originalMouseX = event.clientX;
      window.addEventListener('mousemove', this.resize);
      window.addEventListener('mouseup', this.stopResize);
    },
    resize(event) {
      const dx = event.clientX - this.originalMouseX;
      const dy = event.clientY - this.originalMouseY;
      const aspectRatio = this.originalWidth / this.originalHeight;

      if (Math.abs(dx) > Math.abs(dy)) {
        this.width = this.originalWidth + dx;
        this.height = this.width / aspectRatio;
      } else {
        this.height = this.originalHeight + dy;
        this.width = this.height * aspectRatio;
      }
    },
    stopResize() {
      window.removeEventListener('mousemove', this.resize);
      window.removeEventListener('mouseup', this.stopResize);
      this.imagePaste = new Object({ src: this.imageData, width: this.width });
    },
    saveImage() {
      this.imagePaste = new Object({ src: this.imageData, width: this.width });
      this.$emit('save-image', this.imagePaste);
      this.imageData = '';
      this.imagePaste = ''; // Clear input after save
    },
    cancel() {
      this.imageData = null;
      this.imagePaste = '';
      this.$emit('save-image', this.imagePaste);
      this.$refs.myDropzone.removeAllFiles();
    },
  },
  mounted() {
    const img = new Image();
    img.src = this.imageData;
    img.onload = () => {
      const aspectRatio = img.width / img.height;
      if (img.width >= img.height) {
        this.width = 200;
        this.height = 200 / aspectRatio;
      } else {
        this.height = 200;
        this.width = 200 * aspectRatio;
      }
    };
  },
};
</script>
