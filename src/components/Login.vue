<template>
    <div>
        <button v-if="!isLoggedIn" @click="login">Login</button>
        <p v-if="isLoggedIn"> {{ profileData.name }} </p>
        <p> {{ isLoggedIn }} </p>
        <button v-if="isLoggedIn" @click="logout">Logout</button>

    </div>
</template>

<script>
import { OidcClient } from "oidc-client-ts"

const url = window.location.origin

export const settings = {
    authority: "https://gitlab.com",
    client_id: "83e309af7c08c3d78ba11701185ba085a1c868b08279957cacf9a02d82f492a1",
    redirect_uri: url + "/callback",
    post_logout_redirect_uri: url,
    response_type: "code",
    scope: "openid",
    loadUserInfo: true,
};

var client = new OidcClient(settings);
export default {
    data() {
        return {
            isLoggedIn: false,
            profileData: null
        }
    },

    mounted() {
        console.log("mounted")
        if (window.location.pathname == '/callback') {
            client.processSigninResponse(window.location.href)
                .then(
                     (ress) =>{
                        console.log(ress)
                        this.isLoggedIn = true
                        this.profileData = ress.profile
                        window.history.replaceState({}, null, window.location.origin);
                    }
                )
                .catch(function (error) { console.error(error) })
        }
    },

    methods: {
        login(event) {
            client.createSigninRequest({}).then(function (req) {
                window.location = req.url;
            }).catch(function (err) {
                console.error(err);
            });
        },
        logout() {
            this.isLoggedIn = false,
            this.profileData =  null
        }
    }
}
</script>

<style lang="scss" scoped>
</style>
