<template>
  <div id="app">
    <div v-if="!signedIn">
      <amplify-authenticator></amplify-authenticator>
    </div>
    <div v-if="signedIn">
      <amplify-sign-out class="signout"></amplify-sign-out>


      <div class="container">
        <amplify-photo-picker v-bind:photoPickerConfig="photoPickerConfig">
        </amplify-photo-picker>
        
        <amplify-s3-album path="images/">
        </amplify-s3-album>
      </div>

      <CoffeeShop></CoffeeShop>


    </div>
  </div>
</template>

<script>
import { Auth, Storage } from 'aws-amplify'
import { AmplifyEventBus } from 'aws-amplify-vue'
import CoffeeShop from './components/Coffeshop'

const photoPickerConfig = {
  path: 'images/'
}

Storage.list('images/')
  .then(data => console.log('images from S3: ', data))
  .catch(err => console.log('error'))

export default {
  name: 'app',
  components : { CoffeeShop },
  data () {
    return {
      signedIn: false,
      photoPickerConfig,
    }
  },
  async beforeCreate() {
    try {
      await Auth.currentAuthenticatedUser()
      this.signedIn = true
    } catch (error) {
      this.signedIn = false
    }

    AmplifyEventBus.$on('authState', info => {
      if (info=== 'signedIn' ) this.signedIn = true
      else this.signedIn = false
    })
  },
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.signout {
  background-color: #ededed;
  margin: 0;
  padding: 11px 0px 1px;
}

.container {
  padding: 40px;
}
</style>
