<template>
  <div class="hello">

    <table border="1" align="center" class="prices">

      <td>
        <tr>
          название книги/магазина
        </tr>
        <tr v-for="(book, i) in endjson[0].books" :key="i">
          <th >{{book.info.title}}</th>
        </tr>
      </td>

      <td v-for="(shopTitle, index) in endjson" :key="index" >
        <th>
          {{shopTitle.shop}}
        </th>
        <tr v-for="(book, i) in shopTitle.books" :key="i">
          <td >{{book.price}}</td>
        </tr>
      </td>
    </table>

    <button v-for="(shopTitle, index) in endjson" :key="index" >{{ shopTitle.shop }}</button>

      <br><br>

    <select v-model="active">
      <option v-for="(shopTitle, index) in endjson" :key="index" >{{ shopTitle.shop }}</option>
    </select>

      <br><br>

    <label v-for="(shopTitle, index) in endjson" :key="index" >
      <input type="radio" name="type" :value="shopTitle.shop" v-model="active">
      {{ shopTitle.shop }}
    </label>

  </div>
</template>

<script>
import api from '@/api'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      prices: api.prices,
      authors: api.books.authors,
    }
  },

  computed: {
    endjson: function (){
      const resultArr=[]
      for(const elem of this.prices) {
        let existElem = false
        resultArr.forEach(e=>{
          if(e.shop===elem.shop) {
            existElem = true
            e.books.push({
              'info':this.bookTitle(this.authors, elem.book_id), 
              'price':elem.price})
          }
        })
        if(!existElem) resultArr.push({
          'shop': elem.shop, 
          'books':[{
            'info':this.bookTitle(this.authors, elem.book_id), 
            'price':elem.price,
            }
          ]}) 
      }
      return resultArr
    }
  },

  methods: {

    bookTitle(arr2, book_id){
      let result
      for(const elem of arr2){

        elem.books.forEach(e=>{
          if(e.id===book_id) result = {'title': e.title, 'genre': e.genre}
        })
      }
      return result
    }
  }
}
</script>

<style scoped>
.prices {
  margin-bottom: 10px;
  margin-top: 10px;

}

.books {

}
</style>
