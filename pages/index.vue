<template>
  <div class="container">
    <div>
      <div>universal-social-auth with nuxtjs javascript test</div>
      <Logo />

      <br />
      <div class="row">
        <button @click="useAuthProvider('github', null)">
          oAuth Github M1
        </button>
      </div>
      <br />
      <br />
      <div class="row">
        <button @click="useAuthProvider('github', Github)">
          oAuth Github M2
        </button>
        <!-- <button @click="useAuthProvider('twitch', Twitch)">oAuth Twitch M2</button> -->
      </div>
    </div>
  </div>
</template>

<script>
/* eslint-disable no-console */
/* eslint-disable import/no-duplicates */
import Vue from 'vue'
// Button M1
import { Providers } from 'universal-social-auth'
// Button M2
import { Github /* Facebook, Google , Twitter */ } from 'universal-social-auth'
const Twitch = {
  name: 'twitch',
  url: '/auth/twitch',
  authorizationEndpoint: 'https://id.twitch.tv/oauth2/authorize',
  redirectUri: window.location.origin,
  scope: ['user:edit,user:read:email'],
  scopeDelimiter: '%20',
  oauthType: '2.0',
  responseType: 'data',
  responseParams: 'data',
  requiredUrlParams: ['scope'],
  optionalUrlParams: ['display'],
  scopePrefix: 'openid',
  display: 'popup',
  state: true,
  popupOptions: { width: 452, height: 633 },
}
export default Vue.extend({
  data() {
    return {
      responseData: {
        provider: '',
        code: '',
      },
      hash: '',
      data: { tok: '' },
      fauth: false,
      Github,
      Twitch,
      //   Facebook,
      //   Google,
      //   Twitter
    }
  },
  methods: {
    async useAuthProvider(provider, proData) {
      const pro = proData
      const ProData = pro || Providers[provider]
      try {
        const response = await this.$Oauth.authenticate(provider, ProData)
        const rsp = response
        if (rsp.code) {
          this.responseData.code = rsp.code
          this.responseData.provider = provider
          this.useSocialLogin()
        }
      } catch (error) {
        console.log(error)
      }
    },
    useSocialLogin() {
      // otp from input Otp form
      // hash user data in your backend with Cache or save to database
      const pdata = {
        code: this.responseData.code,
        otp: this.data.tok,
        hash: this.hash,
      }
      this.$axios
        .post(
          'https://api.diadal.com.ng/social-login/' +
            this.responseData.provider,
          pdata
        )
        .then(async (response) => {
          // `response` data base on your backend config
          if (response.data.status === 444) {
            this.hash = response.data.hash
            this.fauth = true // Option show Otp form incase you using 2fa or any addition security apply to your app you can handle all that from here
          } else if (response.data.status === 445) {
            // do something Optional
          } else {
            await this.useLoginFirst(response.data.u)
          }
        })
        .catch((err) => {
          console.log(err)
        })
    },
    useLoginFirst(e) {
      // this sample of to pust user data to my store
      // eslint-disable-next-line no-console
      console.log('userdata', e)
    },
  },
})
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}
.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}
.links {
  padding-top: 15px;
}
</style>
