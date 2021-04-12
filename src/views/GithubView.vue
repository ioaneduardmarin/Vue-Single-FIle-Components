<template>
  <div
    id="github-view"
    class="ui container"
  >
    <h1>GitHub Profiles</h1>
    <div class="ui fluid action input inputDiv">
      <input
        v-model="username"
        type="text"
        placeholder="Search..."
        @change="inputValidation"
      >
      <button
        :disabled="isButtonDisabled"
        class="ui button"
        @click="searchUser"
      >
        Search
      </button>
    </div>
    <div class="ui cards cardsDiv">
      <GithubUserCard
        v-for="user in reversedUsers"
        :key="user"
        :user="user"
      />
    </div>
    <div class="notificationDiv">
      <notification-message
        v-if="wasUserFound === false"
        type="error"
        header="Eroare!"
      >
        <p>
          A avut loc o eroare in timpul cautarii.
          Utilizatorul "{{ this.username }}" nu a fost gasit.
        </p>
      </notification-message>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import GithubUserCard from '@/components/GithubUserCard.vue';
import NotificationMessage from '@/components/NotificationMessage.vue';
import axios from 'axios';

export default {
  name: 'GithubView',
  components: {
    GithubUserCard,
    NotificationMessage,
  },
  data() {
    return {
      users: [],
      usernames: [],
      username: '',
      isUsernameNullOrEmpty: false,
      isButtonDisabled: false,
      wasUserFound: null,
    };
  },
  computed: {
    inputValidation() {
      if ((this.username.trim() || '') === '') {
        return false;
      }
      return true;
    },
    reversedUsers() {
      return this.users.slice(0).reverse();
    },
  },
  methods: {
    async searchUser() {
      this.showProperNotification(null);
      if (!this.inputValidation) {
        return;
      }
      if (this.usernames.includes(this.username)) {
        return;
      }
      this.usernames.push(this.username);
      this.isButtonDisabled = true;
      try {
        const response = await axios.get(`https://api.github.com/users/${this.username}`);
        this.users.push(response.data);
        this.showProperNotification(true);
      } catch (err) {
        this.showProperNotification(false);
        this.usernames = this.removeInexistentUsername(this.usernames, this.username);
      }
      this.isButtonDisabled = false;
    },
    removeInexistentUsername(array, value) {
      return array.filter((username) => username !== value);
    },
    showProperNotification(value) {
      this.wasUserFound = value;
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

.inputDiv {
  margin: 16px 0px 16px 0px;
}

.notificationDiv {
  margin: 16px 0px 16px 0px;
}

.cardsDiv {
  margin: 16px 0px 16px 0px;
}
</style>
