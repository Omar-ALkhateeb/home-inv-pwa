<template>
  <div class="container m-auto pt-5 w-100">
    <h1 class="text-center">"Home inventory app v1"</h1>
    <p id="demo"></p>
    <div class="container m-auto w-75">
      <form role="form">
        <table class="table table-striped">
          <thead>
            <div class="form-row">
              <div class="form-group col-md-6">
                <label for="inputName1">Name:</label>
                <input
                  v-model="name"
                  type="text"
                  name="text"
                  class="form-control input-sm"
                  placeholder="Name"
                  id="myInput"
                />
              </div>
              <div class="form-group col-md-6">
                <label for="inputName2">Date:</label>
                <input
                  v-model="date"
                  type="date"
                  name="text"
                  class="form-control input-sm"
                  id="myInput1"
                />
              </div>
            </div>
            <div class="form-row">
              <div class="form-group col-md-6">
                <label for="inputName3">quantity:</label>
                <input
                  v-model="qty"
                  type="number"
                  name="number"
                  class="form-control input-sm"
                  id="myInput2"
                />
              </div>
            </div>
            <button class="btn btn-primary w-100" type="button" @click="addItem();">
              <i class="fa fa-plus">Add</i>
            </button>
          </thead>
        </table>
      </form>
    </div>
    <table class="table table-striped table-bordered">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Name</th>
          <th scope="col">date</th>
          <th scope="col">qty</th>
          <!-- <th scope="col">expired</th> -->
        </tr>
      </thead>
      <tbody id="body" v-for="(item, index) in items" :key="index">
        <tr>
          <td>{{index+1}}</td>
          <td>
            {{item.name}}
            <br />
            <img :src="item.img" width="49" height="49" />
          </td>
          <td>
            <p style="display:inline;" v-if="isItExpired(item.date)">expired</p>
            {{getExpiryDate(item.date)}}
            <p style="display:inline;" v-if="!isItExpired(item.date)">days left</p>
            <p style="display:inline;" v-if="isItExpired(item.date)">ago</p>
          </td>
          <td>
            <input id="num" size="20" type="number" v-model="item.qty" @click="checkItem(index)" />
            <button class="btn btn-outline-danger w-10" type="button" @click="delItem(index);">
              <i class="fa fa-close"></i>
            </button>
          </td>
          <!-- <td v-if="isItExpired(item.date)">yes</td>
          <td v-if="!isItExpired(item.date)">no</td>-->
        </tr>
      </tbody>
    </table>
  </div>

  <!-- <div class="hello">
    <h2>My Fridge</h2>
    <input v-model="name" placeholder="name" />
    <input type="number" v-model="qty" />
    <input type="date" name="expire" v-model="date" />
    <button @click="addItem()">add item</button>
    <ul>
      <li v-for="(item, index) in items" :key="index">
        <p>{{item.name}}</p>
        <p>
          <span v-if="isItExpired(item.date)">expired</span>
          {{getExpiryDate(item.date)}}
          <span v-if="isItExpired(item.date)">days ago</span>
          <span v-if="!isItExpired(item.date)">days left</span>
        </p>
        <input id="num" size="20" type="number" v-model="item.qty" @click="checkItem(index)" />

      </li>
    </ul>
  </div>-->
</template>

<script>
export default {
  name: "list",
  data() {
    return {
      loading: false,
      name: "",
      msg: "fridge inv",
      qty: 1,
      url:
        "https://api.giphy.com/v1/gifs/search?api_key=L8OaLBCzG3O6FdrexueXereAa4olD2Ws&q=",
      date: "",
      img: "",
      items: [
        {
          name: "milk",
          qty: 4,
          date: new Date("9/13/2012"),
          img:
            "https://media2.giphy.com/media/RJhasN5Konz6an51Vs/200.gif?cid=06e866c8c05afa3e81f8608e81e5c638c782549b51e78663&rid=200.gif"
        }
      ]
    };
  },
  methods: {
    isItExpired(date) {
      var expiryDate = Math.ceil(
        (new Date(date) - Date.now()) / (1000 * 60 * 60 * 24)
      );
      if (expiryDate <= 0) return true;
      return false;
    },
    getExpiryDate(date) {
      return Math.ceil(
        Math.abs(new Date(date) - Date.now()) / (1000 * 60 * 60 * 24)
      );
    },
    checkItem(index) {
      console.log(index);
      // console.log(!this.todos[index]["done"]);
      // this.items[index].done = !this.items[index].done;
      this.updateItemLocalStorage();
    },
    async addItem() {
      console.log(this.date);
      this.items.push({
        name: this.name,
        qty: this.qty,
        img: await this.load(this.name),
        date: this.date.toString()
      });
      this.updateItemLocalStorage();
    },
    delItem(index) {
      this.items.splice(index, 1);
      this.updateItemLocalStorage();
    },
    updateItemLocalStorage() {
      console.log(JSON.stringify(this.items));
      localStorage.setItem("itemLocalStorage", JSON.stringify(this.items));
    },
    getItems() {
      // console.log(this.todos);
      var cached = JSON.parse(localStorage.getItem("itemLocalStorage"));
      if (cached) this.items = cached;
      console.log(localStorage.getItem("itemLocalStorage"));
    },
    async getImg() {
      if (this.img != "") {
        //console.log(todo);
        let words = img.split(" ").join("+");
        //   words.reverse();
        //   let word = words[0];
        let gif = await load(words);
      } else console.log("empty");
    },
    async load(word) {
      const fullUrl = `${this.url}${word}`;
      console.log(fullUrl);
      const response = await fetch(fullUrl);
      const json = await response.json();
      let gifs = json.data.map(gif => gif.images.fixed_height.url);
      console.log(gifs);
      return gifs[0];
      //loading = false;
    }
    // beforeMount() {
    //   this.getTodos();
    // }
  },
  mounted() {
    // console.log(this.todos);
    // localStorage.removeItem("todoLocalStorage");
    this.getItems();
    // this.todos = JSON.parse(localStorage.getItem("todoLocalStorage"));
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
input {
  display: block;
  margin: 0 20px;
}

#num {
  width: 35px;
  display: inline;
}
</style>
