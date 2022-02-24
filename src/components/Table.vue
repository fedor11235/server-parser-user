<template>
  <div>

    <table border="1" align="center" class="prices">

      <td>
        <tr>
          название книги/магазина
        </tr>
        <tr v-for="(book, i) in parseTable[0].books" :key="i">
          <th >
            {{book.info.title}} ({{book.info.name}})
          </th>
        </tr>
      </td>

      <td v-for="(col, index) in parseTable" :key="index" >
        <th>
          {{col.shop}}
        </th>
        <tr v-for="book in col.books" :key="book.price_id">
          <td >
            <InputPrice :prices="prices" :price="book.price" :price_id="book.price_id"/>
          </td>
        </tr>
      </td>
    </table>

    <br>
    <br>

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
import InputPrice from '@/components/InputPrice.vue'

export default {
  name: 'Table',
  components: {
    InputPrice
  },
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
    parseTable: function (){
      const { prices, filtreBeard, filtreGenres, parseInfoBook } = this
      const resultArr=[]
      for(const price of prices) {
        let existElemResultArr = false
        resultArr.forEach(elemResultArr=>{
          if(elemResultArr.shop===price.shop) {
            existElemResultArr = true
            elemResultArr.books.push({
              'info':parseInfoBook(price.book_id), 
              'price':price.price,
              "price_id": price.price_id
              })
          }
        })
        if(!existElemResultArr) resultArr.push({
          'shop': price.shop, 
          'books':[{
            'info':parseInfoBook(price.book_id), 
            'price':price.price,
            "price_id": price.price_id
            }
          ]}) 
      }
      if(filtreGenres){
        for(const elemResultArr of resultArr){
          elemResultArr.books = elemResultArr.books.filter(book=>{
            if(filtreGenres==='Любой') return book
            else if(book.info.genre.includes(filtreGenres)) return book
          })
        }
      }

      if(filtreBeard){
        for(const elemResultArr of resultArr){
          elemResultArr.books = elemResultArr.books.filter(book=>{
            if(filtreBeard==='Любой') return book
            else if(book.info.beard=== Number(filtreBeard)) return book
          })
        }
      }
      return resultArr
    },

    genres: function (){
      const { authors } = this
      let resultArr=[]
      for(const author of authors){
        author.books.forEach(book => {
          resultArr.push(...book.genre.split(', '))
        })   
      }
      resultArr = [...new Set(resultArr)]
      return resultArr
    },

  },

  methods: {
    parseInfoBook(book_id){
      const { authors } = this
      let result
      for(const author of authors){
        author.books.forEach(book=>{
          if(book.id===book_id) 
            result = {
              'title': book.title, 
              'genre': book.genre.split(', '),
              'beard':author.beard, 
              'name':author.name,
              'id': book.id}
          })
      }
      return result
    }
  }
}
</script>

<style scoped>
.price-input {
  width: 64%;
  border: 0;
}
.price-buttom {
  width: 34%;
}

th{
  padding:3px;
}

</style>
