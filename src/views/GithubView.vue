<template>
  <div id="github-view" class="ui container">
    <h1>GitHub Profiles</h1>
    <div class="ui fluid action input">
  <input v-model="username" type="text" @change="inputValidation"  placeholder="Search...">
  <div v-if="this.inputValidation === false" class="ui button">Pardon</div>
  <div v-else class="ui button" @click="searchUser">Search</div>
</div>
    <div class="ui cards">
      <GithubUserCard v-for="user in users" :key="user" :user="user"></GithubUserCard>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import GithubUserCard from '@/components/GithubUserCard.vue'
import axios from 'axios'
export default {
  name: 'githubView',
  components: {
    GithubUserCard
  },
  data: function () {
    return {
      users: [],
      usernames: [],
      username: '',
      isUsernameNullOrEmpty: false
    }
  },
  methods: {
    searchUser: function () {
      axios.get(`https://api.github.com/users/${this.username}`)
        .then(response => {
          this.users.push(response.data)
        })
    }
  },
  computed: {
    inputValidation: function () {
      if ((this.username.trim() || '') === '') {
        return false
      }
      return true
    }
  }
}
</script>
