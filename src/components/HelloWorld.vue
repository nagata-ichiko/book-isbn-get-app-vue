<template>
  <form id="search-form">
    <input
      class="form-text"
      type="text"
      id="isbn"
      placeholder="ISBNコードを入力"
      v-model="isbn"
    />
    <button type="button" v-on:click="search()">検索</button>
  </form>
  <div id="app" v-if="jsonItems.length != 0">
    <table border="1" style="margin-left: auto; margin-right: auto">
      <tr>
        <th>title</th>
        <th>isbn10</th>
        <th>isbn13</th>
      </tr>
      <tr v-for="item of jsonItems" v-bind:key="item.volumeInfo.title">
        <td v-if="item.volumeInfo.industryIdentifiers.length == 2">
          {{ item.volumeInfo.title }}
        </td>
        <td v-if="item.volumeInfo.industryIdentifiers.length == 2">
          {{ item.volumeInfo.industryIdentifiers[0].identifier }}
        </td>
        <td v-if="item.volumeInfo.industryIdentifiers.length == 2">
          {{ item.volumeInfo.industryIdentifiers[1].identifier }}
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
import axios from "axios";
import "vue-good-table/dist/vue-good-table.css";
export default {
  data: function () {
    return {
      jsonItems: [],
    };
  },
  methods: {
    search() {
      const code = document.getElementById("isbn").value;
      for (var i = 0; i < 10; i++) {
        axios
          .get(
            "https://www.googleapis.com/books/v1/volumes?q=" +
              code +
              "&projection=FULL&startIndex=" +
              i
          )
          .then((response) => {
            console.log(response.data);
            this.jsonItems = this.jsonItems.concat(response.data.items);
          });
      }
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
