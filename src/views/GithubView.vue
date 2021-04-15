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
      <transition name="fade">
        <notification-message
          v-if="wasUserFound === false"
          type="error"
          header="Eroare!"
        >
          <p>
            {{ errorMessage }}
          </p>
        </notification-message>
      </transition>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import GithubUserCard from '../components/GithubUserCard.vue';
import NotificationMessage from '../components/NotificationMessage.vue';

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
      errorMessage: '',
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
        this.throwProperErrorMessage(err);
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
    throwProperErrorMessage(error) {
      if (error.message.indexOf('404') !== -1) {
        this.errorMessage = `Utilizatorul "${this.username}" nu a fost gasit.`;
      } else {
        this.errorMessage = error.message;
      }
    },
  },
};
</script>

<style scoped>
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

.fade-enter-active {
 animation: fadeIn 0.5s;
}

.fade-enter {
  opacity: 0;
}

@keyframes fadeIn {
  0% {
    opacity: 0;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    opacity: 1;
  }
}
</style>
