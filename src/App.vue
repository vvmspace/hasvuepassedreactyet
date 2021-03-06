<template>
  <div id="app">
    <github-corner/>
    <p>Has Vue passed React yet?</p>
    <template v-if="repos">
      <h1>{{ vueHasPassedReact ? 'YES' : 'NO' }}</h1>
      <p>
        <small v-if="!vueHasPassedReact">
          Only {{ reactStars - vueStars | formatNumber }} {{ reactStars - vueStars === 1 ? 'star' : 'stars'}} away!
        </small>
      </p>
      <ul>
        <li>
          <a :href="repos.vue.url" target="_blank">
            <vue-icon/>
            <span>{{ vueStars | formatNumber }}</span>
            <star-icon/>
          </a>
        </li>
        <li>
          <a :href="repos.react.url" target="_blank">
            <react-icon/>
            <span>{{ reactStars | formatNumber }}</span>
            <star-icon/>
          </a>
        </li>
      </ul>
    </template>
    <template v-else-if="error">
      <h1 class="error">Error</h1>
      <p>
        Couldn't retrieve any data.
        The API rate limits might have kicked in. Just wait a bit and try again.
      </p>
    </template>
    <p v-else>Loading...</p>
  </div>
</template>

<script>
import axios from 'axios'
import GithubCorner from './components/GithubCorner'
import { VueIcon, ReactIcon, StarIcon } from './components/icons'

const FUNCTIONS_ENDPOINT = 'https://wt-13e53fa81a1f88b8fd161c9e57aeaac4-0.sandbox.auth0-extend.com/fetchGithubStars'

export default {
  name: 'App',

  data() {
    return {
      repos: null,
      error: false
    }
  },

  components: {
    VueIcon,
    ReactIcon,
    StarIcon,
    GithubCorner
  },

  mounted() {
    this.fetchRepos()
  },

  computed: {
    vueHasPassedReact() {
      return this.vueStars > this.reactStars
    },

    vueStars() {
      return this.repos.vue.stargazers.totalCount
    },

    reactStars() {
      return this.repos.react.stargazers.totalCount
    }
  },

  filters: {
    formatNumber(number) {
      return new Intl.NumberFormat().format(number)
    }
  },

  methods: {
    async fetchRepos() {
      try {
        const { data: res } = await axios.get(FUNCTIONS_ENDPOINT)

        if (res.errors && res.errors.length) {
          this.error = true
          this.repos = null
          // eslint-disable-next-line
          console.log(res.errors)
        } else {
          this.error = true
          this.repos = res.data
        }
      } catch (err) {
        // eslint-disable-next-line
        console.log(err)
      }
    }
  }
}
</script>

<style>

* {
  box-sizing: border-box;
}

html, body {
  height: 100%;
  margin: 0;
  padding: 0;
}

body {
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
  text-align: center;
  color: #333;
  background: #efefef;
}

#app {
  width: 300px;
  border: 1px solid #dddddd;
  border-radius: 4px;
  background: #ffffff;
  box-shadow: 0 15px 35px rgba(50,50,93,.1), 0 5px 15px rgba(0,0,0,.07);
  overflow: hidden;
}

h1 {
  font-size: 100px;
  margin: 10px 0;
}

ul {
  margin: 0;
  padding: 0;
  display: flex;
}

li {
  list-style-type: none;
  flex: 1;
}

li a {
  display: flex;
  align-items: center;
  justify-content: center;
  border-top: 1px solid #dddddd;
  padding: 10px;
  text-decoration: none;
  color: #333;
}

li:hover {
  background: #eeeeee;
}

li a > svg {
  display: block;
  width: 22px;
}

li a > * {
  margin: 5px;
}

li:last-of-type {
  border-left: 1px solid #dddddd;
}

h1.error {
  font-size: 2em;
}
p {
  padding: 0 1em;
}

</style>
