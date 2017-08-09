<template>
  <div>
    <v-navigation-drawer temporary v-model="drawer" :mini-variant.sync="mini" light>
      <v-list class="pa-0">
        <v-list-item>
          <v-list-tile avatar tag="div">
            <v-list-tile-avatar>
              <img :src="currentUser.photoURL" alt="avatar">
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title> {{ currentUser.displayName }}</v-list-tile-title>
            </v-list-tile-content>
            <v-list-tile-action>
              <v-btn icon @click.native.stop="mini = !mini">
                <v-icon>chevron_left</v-icon>
              </v-btn>
            </v-list-tile-action>
          </v-list-tile>
        </v-list-item>
        <v-list-item>
          <v-list-tile avatar tag="div">
            <v-list-tile-content>
              <v-btn small @click="logout">Sair</v-btn> 
            </v-list-tile-content>
            <v-list-tile-action>
              <v-list-tile-title> </v-list-tile-title>
            </v-list-tile-action>
          </v-list-tile>
        </v-list-item>
      </v-list>
    <v-flex xs12 sm6 offset-sm3>
      <v-card>
        <v-list>
          <v-list-item>
            <router-link to="/">
              <v-list-tile>
                <v-list-tile-content>
                  <v-list-tile-title>Home</v-list-tile-title>
                </v-list-tile-content>
                <v-list-tile-action>
                  <v-icon>home</v-icon>
                </v-list-tile-action>
              </v-list-tile>
            </router-link>
          </v-list-item>
          <v-list-group v-for="item in items" :value="item.active" v-bind:key="item.title">
            <v-list-tile slot="item">
              <v-list-tile-action>
                <v-icon>{{ item.action }}</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>{{ item.title }}</v-list-tile-title>
              </v-list-tile-content>
              <v-list-tile-action>
                <v-icon>keyboard_arrow_down</v-icon>
              </v-list-tile-action>
            </v-list-tile>
            <v-list-item v-for="subItem in item.items" v-bind:key="subItem.title">
              <router-link :to="'/'+ subItem.router">
                <v-list-tile>
                  <v-list-tile-content>
                    <v-list-tile-title>{{ subItem.title }}</v-list-tile-title>
                  </v-list-tile-content>
                  <v-list-tile-action>
                    <v-icon>{{ subItem.action }}</v-icon>
                  </v-list-tile-action>
                </v-list-tile>
              </router-link>
            </v-list-item>
          </v-list-group>
        </v-list>
      </v-card>
    </v-flex>
    </v-navigation-drawer>
    <v-toolbar class="green" dark>
    <v-toolbar-side-icon light @click.native.stop="drawer = !drawer"></v-toolbar-side-icon>
    <v-toolbar-title>SkyHub</v-toolbar-title>
    
    <v-spacer></v-spacer>
    <v-toolbar-side-icon class="hidden-md-and-up"></v-toolbar-side-icon>
    <v-toolbar-items class="hidden-sm-and-down">
      <v-btn flat :href="'#/clientes'">Cliente</v-btn>
      <v-btn flat>Link Two</v-btn>
      <v-btn flat>Link Three</v-btn>
    </v-toolbar-items>
  </v-toolbar>
  <p>{{$data}}</p>
  
  </div>
  
</template>


<script>
  import { mapGetters } from 'vuex'

  export default {
    //name: 'connected-user',
    data () {
      return {
        drawer: null,
        items: [
          { 
            action: 'build', title: 'OS',
            items: [ { title: 'List Item' } ]
          },
          { 
            action: 'code', title: 'Configuração', active: true,
            items: [
              { title: 'Clientes', router: 'clientes' },
              { title: 'Localidades', router: 'localidades' },
              { title: 'Usuarios', router: 'usuarios' },
              { title: 'Bens', router: 'bens' },
              { title: 'Configuração', router: 'config' }
            ]
          }],
        mini: false,
        right: null,
        presenceRef: firebase.database().ref('presence'),
        usersRef: firebase.database().ref('users'),
        channels: '',
        userConts: []        
      }
    },
    created() {
            firebase.database().ref('users').on('value', snapshot => 
                //this.caregarUser
                console.log(snapshot.val()))
            /*firebase.database().ref('users').child(this.currentUser.uid).on('child_added', snapshot => {
                //console.log(snapshot)
                this.caregarUserCont(snapshot.val())
                //this.messagesRef.child(this.currentUser.uid).on('value', snap => {
                //    this.handleNotifications(channelId, snap)
            })*/
        },
    computed: {
      ...mapGetters(['currentUser'])
    },
    mounted () {
      //this.addListeners()
    },
    methods: {
      logout () {                
          this.presenceRef.child(this.currentUser.uid).remove()
          firebase.auth().signOut()
          this.$store.dispatch("setUser", null)
          this.$router.push('/login')
      },
      addListeners () {
        //let ref = this.getMessageRef()
        this.usersRef.child(this.currentUser.uid).on('child_added', snap => {              
            
            //console.log(this.userCont) 
            //this.$store.dispatch("setUserCont", userCont)

        })
      },
      caregarUserCont(data) {
        console.log(data);
        this.userConts = [];
        for (let key in data) {
            this.userConts.push({
                data: data[4],
                
                
            });
        }
        this.userConts.reverse();

        /*var starCountRef = firebase.database().ref('posts/' + postId + '/starCount');
          starCountRef.on('value', function(snapshot) {
          updateStarCount(postElement, snapshot.val());
        });*/
      },
      

    }
  }

</script>

<style scoped>

</style>