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


    <!--
    <h1>{{genres}}</h1>
    -->

    <label v-for="(genre, index) in genres" :key="index" >
      <input type="radio" name="type" :value="genre" v-model="filtreGenres">
      {{ genre }}
    </label>

    <label>
      <input type="radio" name="type" value="Любой" v-model="filtreGenres">
      Любой
    </label>

    <br><br>

    <label>
      <input type="radio" name="type" value="1" v-model="filtreBeard">
      Автор с бородой
    </label>

    <label>
      <input type="radio" name="type" value="0" v-model="filtreBeard">
      Автор без бороды
    </label>

    <label>
      <input type="radio" name="type" value="Любой" v-model="filtreBeard">
      Любой
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
      filtreGenres: null,
      filtreBeard: null,
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
      if(this.filtreGenres){
        for(const elems of resultArr){
          elems.books = elems.books.filter(e=>{
            if(this.filtreGenres==='Любой') return e
            else if(e.info.genre.includes(this.filtreGenres)) return e
          })
        }

      }

      if(this.filtreBeard){
        for(const elems of resultArr){
          elems.books = elems.books.filter(e=>{
            if(this.filtreBeard==='Любой') return e
            else if(e.info.beard=== Number(this.filtreBeard)) return e
          })
        }

      }
      return resultArr
    },

    genres: function (){
      let resultArr=[]
      for(const elem of this.authors){
        elem.books.forEach(elem => {
          resultArr.push(...elem.genre.split(', '))
        })   
      }
      resultArr = [...new Set(resultArr)]
      return resultArr
    },

  },

  methods: {
    bookTitle(arr2, book_id){
      let result
      for(const elem of arr2){

        elem.books.forEach(e=>{
          if(e.id===book_id) result = {'title': e.title, 'genre': e.genre.split(', '),'beard':elem.beard}
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
