<template>


<div>   
    <div>
        <template>
            <v-container fluid>
                <v-layout row wrap justify-center>
                    <v-flex xs12 sm6>
                        <v-card>
                            <v-card dark class="orange">
                                <v-card-text class="text-md-center"><h4 class="white--text">#Sky::Hub#</h4></v-card-text>
                            </v-card>
                            <v-card-text>
                                <v-container fluid>
                                    <v-layout row wrap>
                                        <v-flex xs12>
                                            <v-text-field label="Email" v-model="email" hint="email@exemplo.com" required></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <v-text-field
                                            label="Senha"
                                            hint="At least 6 characters"
                                            v-model="password"
                                            min="6"
                                            :append-icon="e1 ? 'visibility' : 'visibility_off'"
                                            :append-icon-cb="() => (e1 = !e1)"
                                            :type="e1 ? 'password' : 'text'"
                                            counter
                                            ></v-text-field>
                                        </v-flex>
                                    </v-layout>
                                </v-container>
                            </v-card-text>
                            <v-toolbar class="orange" dark><v-spacer></v-spacer>
                                <v-toolbar-title>
                                    <v-btn flat dark @click.native=" login()">
                                        <span>Logar</span>
                                        <v-icon dark>send</v-icon>
                                    </v-btn>
                                </v-toolbar-title>
                            </v-toolbar>
                            <div class="ui message">
                                <br>
                                <h6>Ainda não está cadastrado? <router-link to="/registro">Registre-se</router-link></h6>
                                <br>
                            </div>
                        </v-card>
                    </v-flex>
                </v-layout>
            </v-container>
            <p>{{ $data }}</p>
        </template>
    </div>
    <v-footer class="indigo">
      <span class="white--text">© 2017</span>
    </v-footer>
</div>
</template>

<script>
export default {
        name: 'login',
        data() {
            return {
                email: '',
                password: '',
                errors: [],
                isLoading: false,
                rules: {
                required: (value) => !!value || 'Obrigatório.',
                    email: (value) => {
                        const pattern = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
                        return pattern.test(value) || 'E-mail invalido.'
                    }
                },
                e1: true,
            }
        },
        computed: {
            temErros () {
                return this.errors.length > 0
            }
        },
        methods: {
            login() {
                console.log("login")
                this.errors = []
                
                if(this.validarForm()){
                    this.isLoading = true

                    firebase.auth().signInWithEmailAndPassword(this.email, this.password).then(user => {

                        this.$store.dispatch('setUser', user)
                        this.$router.push('/')

                    }).catch(error => {
                        this.errors.push(error.message)
                        this.isLoading = false
                    })
                }
            },
            validarForm(){
                if(this.email.length > 0 && this.password.length > 0){
                    return true
                }
                return false
            }
        }
    }
</script>

<style scoped>
     .login__container{
        margin-top: 40px;
    }
    .column{
        max-width: 450px;
    }


</style>