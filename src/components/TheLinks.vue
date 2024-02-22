<script>
export default {
  name: "links",
  props: ["url"],
  data() {
    return {
      links: [],
    };
  },
  mounted() {
    const getLinks = () => {
      const result = document.querySelectorAll("a");
      const links = [];

      for (let item of result) {
        links.push({
          href: item.href,
          content: item.innerText,
        });
      }

      return { links };
    };

    const ref = this;
    const queryOptions = { active: true, lastFocusedWindow: true };
    chrome.tabs.query(queryOptions, function (tabs) {
      const id = tabs[0].id;
      chrome.scripting
        .executeScript({
          target: { tabId: id, allFrames: true },
          func: getLinks,
        })
        .then((injectionResults) => {
          const result = injectionResults[0].result;
          const links = result.links;

          ref.links = links;
        });
    });
  },
};
</script>

<template>
  <div class="d-flex justify-center flex-wrap ga-3">
    <div
      v-if="links"
      style="background-color: lightgrey; padding: 8px"
      class="d-flex flex-column ga-3 w-100"
      v-for="link in links"
    >
      <div>
        <a :href="link.href" target="_blank">{{ link.href }}</a>
        <div>Text: {{ link.content ? link.content : 'empty' }}</div>
      </div>
    </div>
    <div v-else>Ссылок не обнаружено.</div>
  </div>
</template>