<template>
  <form id="search-form">
    <input
      class="form-text"
      type="text"
      id="isbn"
      placeholder="キーワードを入力"
      v-model="isbn"
      style="width: 330px"
    />
    <div>
      <button type="button" v-on:click="search()">検索</button>
      <button type="button" v-on:click="deleteItems()">非選択項目を削除</button>
      <button type="button" v-on:click="getisbnten()">isbn10コピー</button>
      <button type="button" v-on:click="getisbntr()">isbn13コピー</button>
    </div>
  </form>
  <div id="app" v-if="jsonItems.length != 0">
    <table border="1" style="margin-left: auto; margin-right: auto">
      <tr>
        <th>title</th>
        <th>isbn10</th>
        <th>isbn13</th>
        <th>select</th>
      </tr>
      <tr v-for="item of jsonItems" v-bind:key="item.select">
        <td>
          {{ item.title }}
        </td>
        <td>
          {{ item.tenisbn }}
        </td>
        <td>
          {{ item.thrtenisbn }}
        </td>
        <td>
          <input type="checkbox" v-model="item.select" />
        </td>
      </tr>
    </table>
  </div>
</template>
<script>
import axios from "axios";
import "vue-good-table/dist/vue-good-table.css";

class bookitem {
  constructor(ti, te, th, flg) {
    this.title = ti;
    this.tenisbn = te;
    this.thrtenisbn = th;
    this.select = flg;
  }
}
export default {
  data: function () {
    return {
      jsonItems: [bookitem],
    };
  },
  methods: {
    search() {
      this.jsonItems = [];
      const code = document.getElementById("isbn").value;
      for (var i = 0; i < 400; i += 40) {
        axios
          .get(
            "https://www.googleapis.com/books/v1/volumes?q=" +
              code +
              "&projection=FULL&maxResults=40&orderBy=relevance&startIndex=" +
              i
          )
          .then((response) => {
            for (const element of response.data.items) {
              if (element.volumeInfo.industryIdentifiers == undefined) return;
              if (element.volumeInfo.industryIdentifiers.length == 1) continue;
              this.jsonItems.push(
                new bookitem(
                  element.volumeInfo.title,
                  element.volumeInfo.industryIdentifiers[0].identifier,
                  element.volumeInfo.industryIdentifiers[1].identifier,
                  true
                )
              );
            }
          });
      }
    },
    deleteItems() {
      var buf = [];
      for (var i in this.jsonItems) {
        if (this.jsonItems[i].select) {
          buf.push(this.jsonItems[i]);
        }
      }
      this.jsonItems = buf;
    },
    getisbnten() {
      var buf = "";
      for (var i in this.jsonItems) {
        if (this.jsonItems[i].select) {
          buf += this.jsonItems[i].tenisbn + "\n";
        }
      }
      navigator.clipboard.writeText(buf).then(
        () => {
          alert("コピーに成功しました。");
        },
        () => {
          alert("コピーに失敗しました。");
        }
      );
    },
    getisbntr() {
      var buf = "";
      for (var i in this.jsonItems) {
        if (this.jsonItems[i].select) {
          buf += this.jsonItems[i].thrtenisbn + "\n";
        }
      }
      navigator.clipboard.writeText(buf).then(
        () => {
          alert("コピーに成功しました。");
        },
        () => {
          alert("コピーに失敗しました。");
        }
      );
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
