<template>
  <div class="Attractions">
    <loading :active.sync="isLoading"></loading>
    <div id="header">
      <header>
        <h1>高雄旅遊資訊網</h1>
        <select v-model="currentAttraction">
          <option value="">請選擇地區</option>
          <option :value="item" v-for="item of area" :key="item.id">{{ item }}</option>
        </select>
      </header>
    </div>
    <div id="content">
      <div class="container">
        <div class="result">
          <div
            class="card"
            v-for="attraction in filterData[currentPage]"
            :key="attraction.Id"
          >
            <div
              class="card-top"
              :style="`background-image:url(${attraction.Picture1})`"
            >
              <h2>{{ attraction.Name }}</h2>
              <h3>{{ attraction.Zone }}</h3>
            </div>
            <div class="card-text">
              <p>{{ attraction.Opentime }}</p>
              <p>{{ attraction.Add }}</p>
              <a :href="`tel:${attraction.Tel}`">{{ attraction.Tel }}</a>
            </div>
          </div>
        </div>
        <ul class="pagination">
          <li class="page-item">
            <a class="page-link" href="#"><i class="fas fa-caret-left"></i></a>
          </li>
          <li
            class="page-item"
            :class="{ active: currentPage === page - 1 }"
            v-for="page in filterData.length"
            :key="page"
          >
            <a class="page-link" href="#" @click.prevent="currentPage = page - 1">{{
              page
            }}</a>
          </li>
          <li class="page-item">
            <a class="page-link" href="#"><i class="fas fa-caret-right"></i></a>
          </li>
        </ul>
      </div>
    </div>
    <p class="source">資料來源：高雄市政府</p>
    <div class="goTop" :class="{ 'show': show }" @click="goTop()">
      <i class="fas fa-chevron-up"></i>
      <small>TOP</small>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      isLoading: false,
      attractions: [],
      area: [],
      currentPage: 0,
      currentAttraction: '',
      show: false
    }
  },
  created () {
    this.getData()
    window.addEventListener('scroll', this.showGotop)
  },
  methods: {
    getData () {
      const url = 'https://raw.githubusercontent.com/hexschool/KCGTravel/master/datastore_search.json'
      this.$http.get(url)
        .then(res => {
          this.attractions = res.data.result.records
          this.attractions.forEach((items) => {
            if (this.area.indexOf(items.Zone) === -1) {
              this.area.push(items.Zone)
            }
          })
        })
    },
    showGotop () {
      if (window.pageYOffset > 400) {
        this.show = true
      } else {
        this.show = false
      }
    },
    goTop () {
      window.scrollTo({
        top: 0,
        behavior: 'smooth'
      })
    }
  },
  computed: {
    filterData () {
      let items = []
      if (this.currentAttraction !== '') {
        items = this.attractions.filter((item) => {
          return item.Zone === this.currentAttraction
        })
      } else {
        items = this.attractions
      }
      const newData = []
      items.forEach((item, i) => {
        if (i % 10 === 0) {
          newData.push([])
        }
        const page = parseInt(i / 10)
        newData[page].push(item)
      })
      return newData
    }
  }
}
</script>

<style lang="scss">
@import '../assets/scss/all.css';
</style>
