<script>
export default {
name: 'pageSpeed',
props: ['currentPageURL'],

mounted() {
    const ref = this;
    function setUpQuery() {
        const api = 'https://www.googleapis.com/pagespeedonline/v5/runPagespeed';
        const parameters = {
            url: encodeURIComponent(ref.currentPageURL)
        };
        let query = `${api}?`;
        for (let key in parameters) {
            query += `${key}=${parameters[key]}`;
        }
        return query;
    }

    const url = setUpQuery();
    fetch(url)
    .then(response => {
        console.log(response);
        return response.json()
    })
    .then(json => {
      console.log(json);
    });
}}
</script>

<template>
    <v-btn>Тест</v-btn>
</template>