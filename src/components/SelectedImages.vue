<script setup>
defineProps({
  selectedImages: {
    type: Array,
    required: true,
  },
})
</script>
<template>
  <div
    class="modal fade"
    id="selectedImagesModal"
    tabindex="-1"
    aria-labelledby="selectedImagesModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="exampleModalLongTitle">Selected images</h5>
          <button
            type="button"
            class="btn-close"
            data-bs-dismiss="modal"
            aria-label="Close"
          ></button>
        </div>
        <div class="modal-body">
          <table class="table w-max mt-3 p-3 bg-blend-color">
            <thead>
              <tr>
                <th scope="col">ID</th>
                <th scope="col">Author</th>
                <th scope="col">Width</th>
                <th scope="col">Heigth</th>
                <th scope="col">Preview</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="image in selectedImages" :key="`selected${image.id}`">
                <th scope="row">{{ image.id }}</th>
                <td>{{ image.author }}</td>
                <td>{{ image.width }}</td>
                <td>{{ image.height }}</td>
                <td>
                  <img :src="image.download_url" height="50px" width="50px" loading="lazy" />
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>
