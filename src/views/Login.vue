<template>
    <div class="login">
        <label for="login">Username</label>
        <input v-model="loginData.username" id="login-input" type="username">
        <label  for="password">Password</label>
        <input v-model="loginData.password" id="login-input" type="password">
        <button id="login-button" class="button" @click=submitLoginInfo>Login</button>
        <WarningMessage v-if="this.warningMessage.switch" v-bind:warningMessage="this.warningMessage.text" />
    </div>
</template>

<script>

import WarningMessage from '../components/WarningMessage'

export default {
    name: 'Login',
    components: {WarningMessage},
    data() {
        return {
            loginData: {
                username: '',
                password: ''
            },

            warningMessage: {
                switch: false,
                text: ''
            }
        }
    },

    methods: {
        changeRoute() {
            this.$router.push('/home');
        },

        validateUser: async function() {
            const url = "http://localhost:8080/user/connected";
            return fetch(url, { method: 'GET' }).then(res => res.json());
        },

        submitLoginInfo: async function() {
            const username = this.loginData.username;
            const password = this.loginData.password;

            if(username.length === 0) {
                this.warningMessage.switch = true;
                this.warningMessage.text = "Please enter username"
            } else if(password.length === 0) {
                this.warningMessage.switch = true;
                this.warningMessage.text = "Please enter password"
            } else {
                this.sendData(username, password);
            }

            
        },

        async sendData(username, password) {
            const url = `http://localhost:8080/user/${username}&${password}`;
            const data = {
                method: 'POST'
            }

            const response = await fetch(url, data)
                                .then(res => res.json());

            console.log(response);

            if(response.connected) {
                this.changeRoute();
            } else {
                this.warningMessage.switch = true;
                this.warningMessage.text = "Wrong username or password"
            }
        }
    }
}
</script>

<style>
    .login {
        display: flex;
        flex-direction: column;
        align-items: center;
        background-color: lightgray;
        margin-top: 20px;
        padding-top: 10px;
        padding-bottom: 30px;
    }

    #login-input {
        border: 10px;
        border-color: green;
        width: 250px;
    }

    #login-button {
        margin-right: 0px;
    }

    label {
        font-family: 'Roboto', sans-serif;
        font-size: 17px;
    }
</style>