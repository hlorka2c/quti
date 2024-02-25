<script>
import SummaryItem from "./SummaryItem.vue";

export default {
  name: "summary",
  components: {
    SummaryItem,
  }, 
  props: ["url"],
  data() {
    return {
      counts: {},
      headers: {},
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
      const description = document.querySelector(
        'meta[name="description"]'
      )?.content;
      const canonical = document.querySelector("link[rel='canonical']")?.href;
      const robotsTag = document.querySelector('meta[name="robots"]')?.content;
      const keywords = document.querySelector('meta[name="keywords"]')?.content;

      return { title, description, canonical, robotsTag, keywords };
    };

    const getHeaders = () => {
      const result = document.querySelectorAll("h1, h2, h3, h4, h5, h6");
      const headers = [];
      for (let item of result) {
        headers.push({
          node: item.nodeName,
          content: item.innerText,
        });
      }

      return { headers };
    };

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
          ref.items.title.body = result.title || "empty";
          ref.items.description.body = result.description || "empty";
          ref.items.URL.body = ref.url || "empty";
          ref.items.canonical.body = result.canonical || "empty";
          ref.items.robotsTag.body = result.robotsTag || "empty";
          ref.items.keywords.body = result.keywords || "empty";
        });
    });

    chrome.tabs.query(queryOptions, function (tabs) {
      const id = tabs[0].id;
      chrome.scripting
        .executeScript({
          target: { tabId: id, allFrames: true },
          func: getHeaders,
        })
        .then((injectionResults) => {
          const result = injectionResults[0].result;
          ref.headers = result.headers;

          const counts = {};
          const nodes = [];
          ref.headers.forEach(function (header) {
            nodes.push(header.node);
          });
          nodes.forEach(function (node) {
            counts[node] = (counts[node] || 0) + 1;
          });
          ref.counts = counts;
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
    <div v-if="headers">
      <v-table>
        <thead>
          <tr>
            <th class="text-center" v-for="(value, name) in counts">
              {{ name }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr>
            <td class="text-center" v-for="(value, name) in counts">
              {{ value }}
            </td>
          </tr>
        </tbody>
      </v-table>
      <v-table>
        <thead>
          <tr>
            <th class="text-left">Header</th>
            <th class="text-left">Content</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="header of headers">
            <td>{{ header.node }}</td>
            <td>{{ header.content }}</td>
          </tr>
        </tbody>
      </v-table>
    </div>
    <div v-else>Заголовков не обнаружено</div>
  </div>
</template>