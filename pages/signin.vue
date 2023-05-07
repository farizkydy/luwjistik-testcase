<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline"> Hi, Mate! </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-text-field
              v-model="userName"
              type="text"
              label="User Name"
              required
              :rules="usernameRules"
            ></v-text-field>
            <v-text-field
              v-model="password"
              label="Password"
              required
              :rules="passwordRules"
              :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
              :type="show ? 'text' : 'password'"
              @click:append="show = !show"
            ></v-text-field>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn
            color="primary"
            :disabled="!valid"
            :loading="loading"
            @click="signin()"
          >
            Sign In
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-col>
    <v-snackbar v-model="snackbar">
      {{ errorMessage }}

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
          Close
        </v-btn>
      </template>
    </v-snackbar>
  </v-row>
</template>

<script>
import axios from 'axios'

export default {
  name: 'SigninPage',
  data() {
    return {
      valid: false,
      userName: '',
      password: '',
      show: false,
      errorMessage: '',
      loading: false,
      snackbar: false,
      usernameRules: [
        (v) => !!v || 'User Name is required.',
        (v) => (v && v.length >= 3) || 'User Name must be atleast 3 characters.',
      ],
      passwordRules: [
        (v) => !!v || 'Password is required.',
        (v) => (v && v.length >= 6) || 'Password must be atleast 6 characters.',
      ],
    }
  },
  created() {
    if (process.client) {
      if (localStorage.getItem('session') !== null) {
        this.$router.push({
          name: 'profile',
        })
      }
    }
  },
  methods: {
    async signin() {
      const data = {
        username: this.userName,
        password: this.password,
      }
      try {
        this.loading = true
        let res = await axios.post('/api/login', data)
        console.log(res)
        localStorage.setItem('username', this.userName)
        localStorage.setItem('session', res.data.session)
        this.$router.push({
          name: 'profile',
        })
      } catch (error) {
        this.errorMessage = error.response.data.error.errors[0]
        this.snackbar = true
      } finally {
        this.loading = false
      }
    },
  },
}
</script>
