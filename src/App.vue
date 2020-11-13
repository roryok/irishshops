<style>

  body {
    padding: 0;
    margin: 0;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    display: flex;
    flex-direction: column;
  }

  a,a:visited {
    color: #44A;;
    line-height: 1em;
  }

  .top-link {
    text-decoration: none;
  }

  .search {
    /* border-radius: 10px; */
    padding: 1em;
    font-size: 1em;
    margin-bottom: 1em;
    max-width: 600px; 
    margin: 1em auto;
    border-radius: 15px;
  }

  .categories {
    display:flex;
    align-items: center;
    flex-wrap: wrap;
    text-align: center;
    width: 600px;
    margin: auto;
  }

  .cat {
    text-align: center;
    padding: 0.5em;
  }

  .link {
    text-align: left;
  }

  .tagbutton {
    color: #44A;
    border: 0;
    background: transparent;
    font-size: 1em;
    text-decoration: underline;
    text-shadow: 0 0 2px #44A;
  }

  .selected-tag {
    background:teal;
    color: white;
    display: inline-block;
    padding: 0.5em;
    border-radius: 15px;
  }

  .selected-tag a {
    background: inherit;
    border: 0;
    font-family:monospace;
    text-decoration: none;
    cursor: pointer;
    font-size: 1em;
    color: white;
  }

  .link {
    padding: 0.5em;
    margin: 0.5em;
    border: 1px solid #ccc;
    border-radius: 15px;
  }

  .link * {
    padding: 0.5em;
  }

  .desc {
    font-style: italic;
    opacity: 0.8;
  }

  .topbar {
    display: flex;
    padding: 10px 20px;
    background: #44A;
    text-transform: lowercase;
    font-size: small;
  }

  .topbar a {
    color: #fff;
  }

  .top-left {
    margin-left: 0;
    text-decoration: none;
    color: #fff;
  }

  .top-right {
    justify-content: left;
    margin-left: auto;  
  }

  .top-right a {
    text-decoration: none;
    color: #fff;
  }

</style>

<template>
  <div id="app">
    <div class="topbar">
      <router-link to="/" class="top-left">
        Home
      </router-link>    
      <div class="top-right">
        <router-link to="/about">
          about
        </router-link>    
      </div>
    </div>
    <router-link :to="`/`" class="top-link">
      <h1>Irish Shops</h1>
      <span>A simple directory listing of all the irish online stores I could find</span>
    </router-link>
    <!-- route outlet -->
    <div v-if="tag == 'about'">
      <hr>  
      <p>
        I built this halfway through November 2020 as a quick way to categorise online shops.
      </p>
      <p>
        Hopefully it helps somebody!
      </p>
      <p>
        if your shop is not listed here, please contact us and we'll add it
      </p>
    </div>
    <div v-else>  
      <input type="text" class="search" placeholder="search" v-model="search" />
      <div v-if="blank">search by tag:</div>
      <div class="categories">
        <div v-for="tag in tags" :key="tag.name" class="cat">
          <router-link v-if="blank" :to="`/${tag.name}`" :style="`font-size:${(tag.count/30)+1}em`">
            {{ tag.name }} ({{ tag.count }})
          </router-link>
          <h3 v-else class="selected-tag">
            {{ tag.name }}
            <router-link to="/">x</router-link>
          </h3>
          <div v-for="link in links" :key="link.url" class="link">
            <a :href="link.url">{{ link.name || link.url }}</a>
            <div v-if="link.metadesc" class="desc">
              {{ link.metadesc[0] }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { sites } from './sites.json';

export default {
  name: 'App',
  data() {
    return {
      sites,
      search: '',
    };
  },
  methods: {
    clear() {
      this.$route
    }
  },
  computed: {
    blank() {
      return !this.search && !this.$route.params.tag
    },
    tag() {
      // We will see what `params` is shortly
      return this.$route.params.tag
    },
    tags() {
      const tags = {};
      this.sites.forEach((x) => {
        x.tags.forEach((t) => {
          if (!tags[t]) {
            tags[t] = 0;
          }
          tags[t] += 1;
        });
      });
      const y = Object.keys(tags).map((x) => ({ name: x, count: tags[x] }));
      return y.filter((x) => 
        this.blank || 
        (this.search && x.name.includes(this.search)) || 
        (this.tag && x.name.includes(this.tag))
      );
    },
    links() {
      return this.sites.filter((x) => 
        !this.blank && 
        (this.search && x.tags.join(',').includes(this.search)) ||
        (this.tag && x.tags.join(',').includes(this.tag)) 
      );
    },
  },
};
</script>
