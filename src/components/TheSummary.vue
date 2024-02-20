<script>
import { inject } from "vue";
import SummaryItem from "./SummaryItem.vue";

export default {
  name: "summary",
  props: ['url'],
  components: {
    SummaryItem,
  },
  props: ['url'],
  data() {
    return {
      items: {
        title: {
          title: "Title",
        },
        description: {
          title: "Description",
        },
        URL: {
          title: "URL",
        },
        canonical: {
          title: "Canonical",
        },
        robotsTag: {
          title: "Robots Tag",
        },
        keywords: {
          title: "Keywords",
        },
      },
    };
  },

  mounted() {
    const parseSummary = () => {
      const title = document.title;
      const description = document.querySelector('meta[name="description"]')?.content;
      const canonical = document.querySelector("link[rel='canonical']")?.href;
      const robotsTag = document.querySelector('meta[name="robots"]')?.content;
      const keywords = document.querySelector('meta[name="keywords"]')?.content;

      // console.log({title, description, URL, canonical, robotsTag, keywords});

      return {title, description, canonical, robotsTag, keywords};
    }

    const ref = this;

    const queryOptions = { active: true, lastFocusedWindow: true };
    chrome.tabs.query(queryOptions, function (tabs) {
      const id = tabs[0].id;
      chrome.scripting
        .executeScript({
          target: { tabId: id, allFrames: true },
          func: parseSummary,
        })
        .then((injectionResults) => {
          const result = injectionResults[0].result;
          ref.items.title.body = result.title || 'empty';
          ref.items.description.body = result.description || 'empty';
          ref.items.URL.body = ref.url || 'empty';
          ref.items.canonical.body = result.canonical || 'empty';
          ref.items.robotsTag.body = result.robotsTag || 'empty';
          ref.items.keywords.body = result.keywords || 'empty';
        });
      });
    },
  };
</script>

<template>
  <div class="summary">
    <v-list>
      <v-list-item v-for="item in items">
        <summary-item
          :title="item.title"
          :extra="item.extra"
          :body="item.body"
        />
      </v-list-item>
    </v-list>
  </div>
</template>