<template>
  <div class="container about">
    <p>{{ msg }}</p>

    <div v-if="loggedinUser">
      <h3>
        Loggedin User:
        {{ loggedinUser.fullname }}
        <button @click="doLogout">Logout</button>
      </h3>
    </div>
    <div v-else>
      <h2>Login</h2>
      <form @submit.prevent="doLogin">
        <select v-model="loginCred.username">
          <option value="">Select User</option>
          <option v-for="user in users" :key="user._id" :value="user.username">
            {{ user.fullname }}
          </option>
        </select>
        <button>Login</button>
      </form>


      
      <form @submit.prevent="doSignup">
        <h2>Signup</h2>
        <input
          type="text"
          v-model="signupCred.fullname"
          placeholder="Your full name"
        />
        <input
          type="text"
          v-model="signupCred.username"
          placeholder="Username"
        />
        <input
          type="password"
          v-model="signupCred.password"
          placeholder="Password"
        />
        <button>Signup</button>
      </form>
    </div>
    <hr />
    <details>
      <summary>Admin Section</summary>
      <ul>
        <li v-for="user in users" :key="user._id">
          <pre>{{ user }}</pre>
          <button @click="removeUser(user._id)">x</button>
        </li>
      </ul>
    </details>
  </div>
</template>

<script>
// import ImgUploader from '../cmps/ImgUploader.vue'

export default {
  name: 'login-signup',
  data() {
    return {
      msg: '',
      loginCred: { username: 'user1', password: '123' },
      signupCred: {
        username: '',
        password: '',
        fullname: '',
        imgUrl: '',
        stations: [],
      },
    }
  },
  computed: {
    users() {
      return this.$store.getters.users
    },
    loggedinUser() {
      return this.$store.getters.loggedinUser
    },
  },
  created() {
    this.loadUsers()
  },
  methods: {
    async doLogin() {
      if (!this.loginCred.username) {
        this.msg = 'Please enter username/password'
        return
      }
      try {
        await this.$store.dispatch({ type: 'login', userCred: this.loginCred })
        this.$router.push('/station/collection')
      } catch (err) {
        console.log(err)
        this.msg = 'Failed to login'
      }
    },
    doLogout() {
      this.$store.dispatch({ type: 'logout' })
    },
    async doSignup() {
      if (
        !this.signupCred.fullname ||
        !this.signupCred.password ||
        !this.signupCred.username
      ) {
        this.msg = 'Please fill up the form'
        return
      }
      await this.$store.dispatch({ type: 'signup', userCred: this.signupCred })
      this.$router.push('/station/collection')
    },
    loadUsers() {
      this.$store.dispatch({ type: 'loadUsers' })
    },
    async removeUser(userId) {
      try {
        await this.$store.dispatch({ type: 'removeUser', userId })
        this.msg = 'User removed'
      } catch (err) {
        this.msg = 'Failed to remove user'
      }
    },
    onUploaded(imgUrl) {
      this.signupCred.imgUrl = imgUrl
    },
  },
  components: {
    // ImgUploader,
  },
}
</script>
