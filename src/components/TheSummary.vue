<script>
import { inject } from "vue";
import SummaryItem from "./SummaryItem.vue";

export default {
  name: "summary",
  components: {
    SummaryItem,
  },
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
      },
    };
  },

  mounted() {
    const getTitle = () => {
      return document.title;
    };

    const parseSummary = () => {
      const title = document.title;
      const description = document.querySelector('meta[name="description"]')?.content;
      const URL = document.URL;
      const canonical = document.querySelector("link[rel='canonical']")?.content;
      const robotsTag = document.querySelector('meta[name="robots"]')?.content;

      console.log({title, description, URL, canonical, robotsTag});

      return {title, description, URL, canonical, robotsTag};
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
          ref.items.title.body = result.title;
          ref.items.description.body = result.description;
          ref.items.URL.body = result.URL;
          ref.items.canonical.body = result.canonical || 'empty';
          ref.items.robotsTag.body = result.robotsTag || 'empty';



        });
    });
  },
  // mounted() {
  //   const ref = this;
  //   const queryOptions = { active: true, lastFocusedWindow: true };
  //   chrome.tabs.query(queryOptions, function (tabs) {
  //     const tabId = tabs[0].id;
  //     ref.execScript(tabId, ref.getTitle, (injectionResults) => {
  //       for (const { frameId, result } of injectionResults) {
  //         alert(`Frame ${frameId} result: ${result}`);
  //       }
  //     });
  //   });
  // },
  // methods: {
  //   getTitle() {
  //     return document.title;
  //   },

  //   execScript(id, mainFunc, callback) {
  //     chrome.scripting
  //       .executeScript({
  //         target: { tabId: id, allFrames: true },
  //         func: mainFunc,
  //       })
  //       .then(callback);
  //   },

  //   // getSummary() {
  //   //   const summary = {};
  //   //   const title = document.querySelector("title");
  //   //   summary.title = {
  //   //     extra: title.innerText.length() + "characters",
  //   //     body: title.innerText,
  //   //   };

  //   //   const description = document.querySelector('meta[name="description"]');
  //   //   summary.description = {
  //   //     extra: description.content.length(),
  //   //     body: description.content,
  //   //   };

  //   //   const url = document.URL;
  //   //   summary.URL = {
  //   //     body: url,
  //   //   };

  //   //   const canonical = document.querySelector('link[rel="canonical"]');
  //   //   summary.canonical = {
  //   //     body: canonical,
  //   //   };

  //   //   const robotsTag = document.querySelector('meta[name="robots"]');
  //   //   summary.robotsTag = {
  //   //     body: robotsTag.content,
  //   //   };

  //   //   alert(summary.description)

  //   //   return summary;
  //   // },

  //   // onResult(frames) {
  //   //   if (!frames || !frames.length) {
  //   //     return;
  //   //   }

  //   //   // const pageTitles = frames.map(frame=>frame.result)
  //   //   //   .reduce((r1,r2)=>r1.concat(r2));

  //   //   alert(JSON.stringify(frames))

  //   //   alert('work');

  //   //   // this.items.title.extra = result.title.extra || "";
  //   //   // this.items.title.body = result.title.body || "";

  //   //   // this.items.description.extra = result.description.extra || "";
  //   //   // this.items.description.body = result.description.body || "";

  //   //   // this.items.URL.body = result.URL.body || "";

  //   //   // this.items.canonical.body = result.canonical.body || "";

  //   //   // this.items.robotsTag.body = result.robotsTag.body || "";
  //   // },
  // },
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