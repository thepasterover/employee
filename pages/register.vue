<template>
  <div class="mt-10">
      <v-row justify="center" align="center">
      <v-card height="500" width="500" elevation="0">
        <div v-if="!$auth.loggedIn" >
          <v-row justify="center">
              <img src="~/assets/images/logo.png" alt="as_creations_company_logo" height="100px" width="200px">
          </v-row>
        </div>
          <v-row justify="center" class="mt-n2" :class="{'mt-16 mb-5 pt-15': $auth.loggedIn}">
              <v-card-title class="text-h5">Register as an Employee</v-card-title>
          </v-row>
          <div class="pa-6 " >
              <v-form ref="form">
                <v-text-field 
                v-model="firstName" 
                color="appmainblue" 
                label="First Name"
                :rules="[rules.required, rules.max]" 
                >
                </v-text-field>
                <v-text-field 
                v-model="lastName" 
                color="appmainblue" 
                label="Last Name"
                :rules="[rules.required, rules.max]" 
                >
                </v-text-field>
                <v-text-field 
                v-model="email" 
                color="appmainblue" 
                label="Email" 
                :rules="[rules.required, rules.email]" 
                >
                </v-text-field>
                  <v-text-field 
                  v-model="password" 
                  color="appmainblue" 
                  label="Password" 
                  :rules="[rules.required, rules.min, rules.passwordRegex]"
                  :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                  :type="show ? 'text' : 'password'"
                  @click:append="show = !show"
                  >
                  </v-text-field>
                  <v-text-field 
                  v-model="confPassword" 
                  color="appmainblue" 
                  label="Confirm Passord" 
                  :rules="[rules.required, rules.min, rules.matchPassword,]"
                  :append-icon="show ? 'mdi-eye' : 'mdi-eye-off'"
                  :type="show ? 'text' : 'password'"
                  @click:append="show = !show"
                  >
                  </v-text-field>
              </v-form>
              <v-btn @click="submit"  color="appmainblue" depressed class="white--text">Submit</v-btn>
          </div> 
      </v-card>
      </v-row>
      <v-snackbar
      v-model="snackbar"
      :timeout="4000"
      :color="snackbarColor"
      top
      elevation=0
      app
      >
      {{message}}
      <template v-slot:action="{ attrs }">
        <v-icon
        color="white"
        size=23
        v-bind="attrs"
        @click="snackbar = false">
            mdi-close-circle
        </v-icon>
      </template>
        
      </v-snackbar>
    </div>
</template>

<script>
export default {
  layout: 'nonlogged',
  head() {
    return {
      title: 'Register',
    }
  },
  data() {
    return {
      firstName: null,
      lastName: null,
      email: null,
      password: null,
      confPassword: null,
      snackbar: false,
      snackbarColor: '#73cfa6',
      message: null,
      show: false,
      rules: {
        required: v => !!v || 'Required Field',
        min: v => (v || '').length >= 8 || 'Min 8 characters',
        matchPassword: () => (this.password === this.confPassword) || 'Passwords do not match',
        passwordRegex: v => {
          let pattern = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z\d]{8,}$/
          return pattern.test(v) || 'Min 8 Characters, atleast 1 uppercase, lowercase character and a number!'
        },
        max: v => (v || '').length <= 30 || 'Max 30 characters',
        email: v => {
            let pattern = /\S+@\S+\.\S+/
            return pattern.test(v) || 'Enter a valid email!'
        }
      }
    }
  },
  async created() {
    this.$store.commit('head/set', 'Register')
  },
  methods: {
    async submit(){
      try {
        if(this.$refs.form.validate()){
          let data = await this.$axios.$post('auth/employee/register', {
            name: this.firstName + ' ' + this.lastName,
            email: this.email,
            password: this.password,
          })
          if(data.message.flag){
            this.message = data.message
            this.snackbarColor = '#73cfa6'
            this.snackbar = true
            setTimeout(() => {
              this.$router.push('/login')
            }, 3500)
          }
        }
      } catch(err) {
        if(err.response){
          this.message = err.response.data.error
          this.snackbarColor = 'error'
          this.snackbar = true
        } else {
          console.log(err)
        }
      }
    }
  }
}
</script>