<template>
  <div id="app">
    <button class="button is-spotify" @click="getAuth()">
      Login to Spotify
    </button>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  methods: {
    login (callback) {
      var CLIENT_ID = '6d03b3388c6044cb92c58978bf82f750'
      var REDIRECT_URI = 'http://localhost:8081'

      function getLoginURL (scopes) {
        return 'https://accounts.spotify.com/authorize?client_id=' + CLIENT_ID +
          '&redirect_uri=' + encodeURIComponent(REDIRECT_URI) +
          '&scope=' + encodeURIComponent(scopes.join(' ')) +
          '&response_type=token'
      }

      var url = getLoginURL([
        'user-read-email'
      ])

      var width = 450
      var height = 730
      var left = (screen.width / 2) - (width / 2)
      var top = (screen.height / 2) - (height / 2)

      window.addEventListener('message', function (event) {
        var hash = JSON.parse(event.data)
        if (hash.type === 'access_token') {
          callback(hash.access_token)
        }
      }, false)

      var w = window.open(url,
        'Spotify',
        'menubar=no,location=no,resizable=no,scrollbars=no,status=no, width=' +
        width + ', height=' + height + ', top=' + top + ', left=' + left
      )
      console.log(w)
    },

    getUserData (accessToken) {
      axios({
        method: 'get',
        url: 'https://api.spotify.com/v1/me',
        headers: {
          'Authorization': 'Bearer ' + accessToken
        }
      })
    },

    getAuth () {
      this.login(function (accessToken) {
        this.getUserData(accessToken)
          .then(function (response) {
            console.log(response)
            this.display = true
          })
      })
    }
  }
}
</script>

<style>

</style>
