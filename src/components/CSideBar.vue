<template>
  <v-navigation-drawer v-model="drawer" absolute fixed clipped>
      <v-toolbar dark color="primary">
        <v-btn icon dark @click.native="drawer=false">
          <v-icon>close</v-icon>
        </v-btn>
        <v-toolbar-title>Vueshop</v-toolbar-title>
      </v-toolbar>

      <v-list v-if="guest">
        <v-list-tile>    
          <v-btn
            @click="register()"
            depressed
            block
            round
            color="secondary"
            class="white--text"
          >
            Register
            <v-icon right dark>person_add</v-icon>
          </v-btn>
        </v-list-tile>
        <v-list-tile>    
          <v-btn
            @click="login()"
            block
            round
            depressed
            color="accent lighten-1"
            class="white--text"
          >
            Login
            <v-icon right dark>lock_open</v-icon>
          </v-btn>
        </v-list-tile>
      </v-list>

      <v-list v-if="!guest">
        <v-list-tile>
          <v-list-tile-avatar>
            <img :src="getImage('/users/'+user.avatar)">
          </v-list-tile-avatar>

          <v-list-tile-content>
            <v-list-tile-title>
              {{ user.name }}
            </v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
        <v-list-tile>    
          <v-btn
            block
            small
            round
            depressed
            color="error lighten-1"
            class="white--text"
            @click.stop="logout();"
          >
            Logout
            <v-icon small right dark>settings_power</v-icon>
          </v-btn>
        </v-list-tile>
      </v-list>

      <v-list class="pt-0" dense>
        <v-divider></v-divider>

        <v-list-tile
          v-for="(item,index) in items"
          :key="index"
          :href="item.route" 
          :to="{name: item.route}"
          v-if="!item.auth || (item.auth && !guest)" 
        >
          <v-list-tile-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-tile-action>

          <v-list-tile-content>
            <v-list-tile-title>{{ item.title }}</v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>

    </v-navigation-drawer>
</template>
<script>
import { mapGetters, mapActions } from 'vuex'
export default {
    name: 'c-side-bar',
    data: () => ({
      items: [
          { title: 'Home', icon: 'dashboard', route: 'home' },
          { title: 'Profile', icon: 'person', route: 'profile', auth: true },
          { title: 'My Order', icon: 'shop_two', route: 'my-order', auth: true },
          { title: 'About', icon: 'question_answer', route: 'about' },      
      ]
    }),
    computed: {
      ...mapGetters({
        sideBar     : 'sideBar',
        user        : 'auth/user',
        guest        : 'auth/guest',
      }),
      drawer: {
        get () {
          return this.sideBar
        },
        set (value) {
          this.setSideBar(value)
        }
      },
    },
    methods: {
      ...mapActions({
        setSideBar  : 'setSideBar',
        setStatusDialog   : 'dialog/setStatus',
        setComponent   : 'dialog/setComponent',
        setAuth     : 'auth/set',
        setAlert     : 'alert/set',
      }),
      login(){
        this.setStatusDialog(true)
        this.setComponent('login')
        this.setSideBar(false)
      },
      register(){
        this.setStatusDialog(true)
        this.setComponent('register')
        this.setSideBar(false)
      },
      logout(){
        let config = {
          headers: {
            'Authorization': 'Bearer ' + this.user.api_token,
          },
        }
        this.axios.post('/logout', {}, config)
          .then(() => {
            this.setAuth({})
            this.setAlert({
                status : true,
                text  : 'Logout successfully',
                type  : 'success',
              })
            this.setSideBar(false)
          })
          .catch((error) => {
            let responses = error.response    
            this.setAlert({
                status : true,
                text  : responses.data.message,
                type  : 'error',
            })
          })
      }
    },
}
</script>
