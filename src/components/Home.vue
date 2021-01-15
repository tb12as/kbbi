<template>
  <div class="container">
    <h1>Kamus Besar Bahasa Indonesia</h1>
    <p>Aplikasi KBBI sederhana menggunakan Vuejs dan sebuah public API</p>

    <div class="row justify-content-center">
      <div class="col-md-7">
        <input type="text" v-model="q" class="form-control" placeholder="Cari Kata">

        <div class="card my-4">
          <div class="card-header">
            <h5 class="card-title">Hasil</h5>
          </div>
          <div class="card-body">

            <p v-if="searching">{{ response }}</p>

            <div class="my-2" v-for="(result, index) in results" :key="index">
              <div class="result-list" v-for="(resultList, idn) in result.resultLists" :key="idn">
                <h5>{{ resultList.kata }}</h5>
                <p v-for="(artiList, id) in resultList.arti" :key="id">{{ artiList }}</p>
                <hr>
              </div>
            </div>

          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  import axios from 'axios'
  import _ from 'lodash'

  export default {
    name: 'Home',
    data() {
      return {
        q: '',
        wait: false,
        searching: false,
        response: '',
        results: [],
      }
    },

    watch: {
      q: function() {
        this.searching = true
        this.response = 'Menunggu anda selesai mengetik....'
        this.results = []
        this.debouncedResults()
      },
    },

    created: function() {
      this.debouncedResults = _.debounce(this.loadData, 600)
    },

    methods: {
      loadData() {
        if (this.q !== '') {
          this.response = 'Mencari....'
          axios.get(`https://kbbi-api-amm.herokuapp.com/search?q=${this.q}`)
          .then((res) => {
            if (res.data.status !== false) {
              // console.log(res.data)
              this.results = res.data;
              this.searching = false
            } else {
              this.error = res.data.error;
            }
          })
          .catch((err) => {
            console.log(err)
          })
          // console.log(this.q);
        }
      }
    }


  }
</script>

<style>

</style>