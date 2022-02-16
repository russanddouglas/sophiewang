<template>
  <div id="app">
    <div id="phone">
      <!-- tab -->
      <div class="tabs">
          <div class="tab" :class="{ active: !!isShowTrending }" @click="showTrending">Trending</div>
          <div class="tab" :class="{ active: !!isShowReaction }" @click="showReaction">Reaction</div>
          <div class="tab" :class="{ active: !!isShowSearch }" @click="showSearch">Search</div>
      </div>
      <div class="content">
        <!-- trending -->
        <div v-if="isShowTrending" class="trending-result">
          <div class="trending-result__item" v-for="(item, trendingIdx) in trendingResultList" :key="trendingIdx">
            <img :src="item.gifUrl" alt="">
          </div>
        </div>
        <!-- search -->
        <div v-if="isShowSearch">
          <input type="text" v-model="searchInput"> 
          <div class="search-result">
            <div class="search-result__item" v-for="(item, idx) in searchResultList" :key="idx">
              <img :src="item.gif100px" alt="">
            </div>
          </div>
        </div>
        <!-- reaction -->
        <div v-if="isShowReaction">
          <button v-if="isClickedCategory" @click="backToShowCategory">show category</button>
          <div class="search-result">
            <div class="search-result__item" v-for="(item, categoryIdx) in categoryResultList" :key="categoryIdx">
              <div v-if="!isClickedCategory" class="category" @click="onClickCategory(item.tagText)">
                <img :src="item.gfycats[0].gif100px" alt="">
                <span class="category__text">{{ item.tagText }}</span>
              </div>
              <!-- click a category (for example: `trending`) -->
              <div v-else>
                <img :src="item.gif100px" alt="">
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  components: {},
  mounted() {
    this.getTrending();
    this.getCategory();
  },
  data() {
    return {
      trendingResultList: [],
      searchResultList: [],
      categoryResultList: [],
      searchInput: '',
      isShowTrending: true,
      isShowSearch: false,
      isShowReaction: false,
      isClickedCategory: false,
    };
  },
  watch: {
    searchInput(val) {
      this.searchGfycats(val);
    },
  },
  methods: {
    showTrending() {
      this.isShowSearch = false;
      this.isShowTrending = true;
      this.isShowReaction = false;
    },
    showSearch() {
      this.isShowSearch = true;
      this.isShowTrending = false;
      this.isShowReaction = false;
    },
    showReaction() {
      this.isShowReaction = true;
      this.isShowSearch = false;
      this.isShowTrending = false;
    },
    getCategory() {
      axios.get('https://api.gfycat.com/v1/reactions/populated')
        .then((response) => {
          this.categoryResultList = response.data.tags;
        });
    },
    getTrending() {
      axios.get('https://api.gfycat.com/v1/reactions/populated?tagName=trending')
        .then((response) => {
          this.trendingResultList = response.data.gfycats;
        });
    },
    searchGfycats(searchText) {
      axios.get(`https://api.gfycat.com/v1/gfycats/search?search_text=${ searchText }`)
        .then((response) => {
          // handle success
          console.log(response.data.gfycats);
          this.searchResultList = response.data.gfycats;
        });
    },
    onClickCategory(text) {
      this.isClickedCategory = true;
      axios.get(`https://api.gfycat.com/v1/reactions/populated?tagName=${ text }`)
        .then((response) => {
          this.categoryResultList = response.data.gfycats;
        });
    },
    backToShowCategory() {
      this.getCategory();
      this.isClickedCategory = false;
    },
  }
}
</script>

<style>
html,body {
  padding: 0;
  margin: 0;
}
#app {
  margin: 0;
  background: black;
  display: flex;
  justify-content: center;
  min-height: 100vh;
}
#phone {
  background: white;
  padding: 40px 20px;
  width: 600px;
}
.tabs {
  color: black;
  display: flex;
  flex-wrap: wrap;
}
.tab {
  padding: 10px;
  border: solid 1px;
  cursor: pointer;
}
.tab:hover {
  background: black;
  color: white;
}
.tab.active {
  background: grey;
}
img {
  max-width: 100%;
}
.search-result {
  display: flex;
  flex-wrap: wrap;
}
.search-result__item {
  flex-basis: 50%;
}
.search-result__title {
  font-size: 20px;
}
.category {
  position: relative;
}
.category img {
  width: 100%;
  min-height: 100px;
  object-fit: cover;
}
.category__text {
  position: absolute;
  left: 0;
  top: 0;
  color: white;
  background: black;
}
</style>