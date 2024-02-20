<script>
import TheSummary from "./components/TheSummary.vue";
import ThePageSpeed from "./components/ThePageSpeed.vue";

export default {
  components: {
    TheSummary,
    ThePageSpeed,
  },
  data: () => ({
    tab: null,
    url: '',
  }),
  mounted() {
    const getURL = () => {
      const url = document.URL;
      return {url};
    }
    const ref = this;
    const queryOptions = { active: true, lastFocusedWindow: true };
    chrome.tabs.query(queryOptions, function (tabs) {
      const id = tabs[0].id;
      chrome.scripting
        .executeScript({
          target: { tabId: id, allFrames: true },
          func: getURL,
        })
        .then((injectionResults) => {
          const result = injectionResults[0].result;
          console.log(result.url);
          ref.url = result.url;
        });
    });
  }
};
</script>

<template>
  <v-card>
    <v-tabs v-model="tab" bg-color="primary">
      <v-tab value="summary">Summary</v-tab>
      <v-tab value="pageSpeed">Page Speed</v-tab>
      <v-tab value="images">Images</v-tab>
      <v-tab value="links">Links</v-tab>
    </v-tabs>

    <v-card-item>
      <v-window v-model="tab">
        <v-window-item value="summary">
          <v-card-title>Summary</v-card-title>
          <TheSummary :url="url"></TheSummary>
        </v-window-item>

        <v-window-item value="pageSpeed">
          <v-card-title>Page Speed</v-card-title>
          <ThePageSpeed :currentPageURL="url"></ThePageSpeed>
        </v-window-item>

        <v-window-item value="images">
          <v-card-title>Images</v-card-title>
        </v-window-item>

        <v-window-item value="links">
          <v-card-title>Links</v-card-title>
        </v-window-item>
      </v-window>
    </v-card-item>
  </v-card>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
