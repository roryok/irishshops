<style>
  #app {
    font-family: Calibri, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
    display: flex;
    flex-direction: column;
  }

  a,a:visited {
    color: #1a1;
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

</style>

<template>
  <div id="app">
    <router-link :to="`/`" class="top-link">
      <h1>IrishShops.ie</h1>
      <span>A simple directory listing of all the irish online stores I could find</span>
    </router-link>
    <!-- route outlet -->
    <!-- component matched by the route will render here -->
    <router-view name></router-view>
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
          <div class="desc">
            {{ link.desc }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { sites } from './data.json';

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
