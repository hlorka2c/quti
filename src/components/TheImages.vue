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
      style="width: 100%; background-color: lightgrey; padding: 8px"
      class="d-flex justify-center align-center flex-column ga-3"
      v-for="image in images"
    >
      <img class="w-100" :src="image.src" />
      <div class="w-100">
        <div>Alt: {{ image.alt || 'empty' }}</div>
        <a  target="_blank" :href="image.src">Src: {{ image.src }}</a>
      </div>
    </div>
    <div v-else>Изображений не обнаружено.</div>
  </div>
</template>