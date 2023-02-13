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
    <table style="margin-left: auto; margin-right: auto">
      <tr>
        <th>title</th>
        <th>isbn10</th>
        <th>isbn16</th>
      </tr>
    </table>
    <ul v-for="item of jsonItems" v-bind:key="item.volumeInfo.title">
      <li style="text-align: center">
        {{ item.volumeInfo.title }}
      </li>
    </ul>
  </div>
</template>

<script>
import axios from "axios";
// import VueGoodTablePlugin from "vue-good-table";
// import the styles
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
