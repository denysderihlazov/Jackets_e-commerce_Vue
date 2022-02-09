<template>
    <div class="page-log-in">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">{{ $t('log_in') }}</h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label>{{ $t('username') }}</label>
                        <div class="control">
                            <input type="text" class="input" v-model="username">
                        </div>
                    </div>

                    <div class="field">
                        <label>{{ $t('password') }}</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">{{ $t('log_in') }}</button>
                        </div>
                    </div>

                    <hr>

                    {{ $t('or') }}<router-link to="/sign-up">{{ $t('click_here') }}</router-link> {{ $t('to_sign_up') }}
                </form>
            </div>
        </div>
    </div>
</template>


<script>
import axios from "axios"

export default {
    name: 'LogIn',
    data() {
        return {
            username: '',
            password: '',
            errors: []
        }
    },
    mounted() {
        document.title = 'Log In | Djackets'
    },
    methods: {
        async submitForm() {
            axios.defaults.headers.common['Authorization'] = ""

            localStorage.removeItem("token")

            const formData = {
                username: this.username,
                password: this.password
            }
            await axios
                .post("/api/v1/token/login/", formData)
                .then(response => {
                    const token = response.data.auth_token
                    this.$store.commit('setToken', token)

                    axios.defaults.headers.common["Authorization"] = "Token " + token
                    localStorage.setItem("token", token)
                    const toPath = this.$route.query.to || '/cart'
                    this.$router.push(toPath)
                })
                .catch(error => {
                    if (error.response) {
                        for (const property in error.response.data) {
                            this.errors.push(`${property}: ${error.response.data[property]}`)
                        }
                    } else {
                        this.errors.push('Something went wrong. Please try again')

                        console.log(JSON.stringify(error))
                    }
                })
        }
    }
}
</script>