<script>
export default {
  name: "images",
  data() {
    return {
      images: [],
    };
  },
  mounted() {
    const getImages = () => {
      const result = document.querySelectorAll("img");
      const images = [];

      for (let item of result) {
        images.push({
          src: item.src,
          alt: item.alt,
        });
      }

      return { images };
    };

    const ref = this;
    const queryOptions = { active: true, lastFocusedWindow: true };
    chrome.tabs.query(queryOptions, function (tabs) {
      const id = tabs[0].id;
      chrome.scripting
        .executeScript({
          target: { tabId: id, allFrames: true },
          func: getImages,
        })
        .then((injectionResults) => {
          const result = injectionResults[0].result;
          console.log(result);
          const images = result.images;

          ref.images = images;
        });
    });
  },
};
</script>

<template>
  <div class="d-flex justify-center flex-wrap ga-3">
    <div
      v-if="images"
      style="background-color: white; padding: 8px"
      class="w-100 d-flex justify-center align-center flex-column ga-3"
      v-for="image in images"
    >
      <img class="w-100" style="border: 1px solid blue; background-color: lightslategray;" :src="image.src" />
      <div class="w-100">
        <div style="color: black">Alt: {{ image.alt || 'empty' }}</div>
        <a class="image-src" target="_blank" :href="image.src">Src: {{ image.src }}</a>
      </div>
    </div>
    <div class="error-page text-center h-100 d-flex justify-center align-center" v-else>Изображений не обнаружено.</div>
  </div>
</template>

<style>
.image-src {
  color: blue;
}

.image-src:hover {
  background-color: unset;
  color: darkblue;
}</style>