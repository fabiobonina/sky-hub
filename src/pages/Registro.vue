<template>

<div>
    <div>
        <template>
            <v-container fluid>
                <v-layout row wrap justify-center>
                    <v-flex xs12 sm6>
                        <v-card>
                            <v-card dark class="orange">
                                <v-card-text class="text-md-center"><h5 class="white--text">#Sky::Hub#</h5></v-card-text>
                            </v-card>
                            <v-card-text>
                                <v-container fluid>
                                    <v-layout row wrap>
                                        <v-flex xs12>
                                            <v-text-field label="Usuario" v-model="usuario" required></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <v-text-field label="Nome" v-model="nome" hint="Nome completo" required></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <v-text-field label="E-mail" v-model="email" :rules="[rules.required, rules.email]"></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <v-text-field label="Senha" v-model="password" hint="At least 6 characters" min="6"
                                                :append-icon="e1 ? 'visibility' : 'visibility_off'" :append-icon-cb="() => (e1 = !e1)" :type="e1 ? 'password' : 'text'" counter
                                            ></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <v-text-field label="Confimar Senha" v-model="password_confirmation" hint="At least 6 characters" min="6"
                                                :append-icon="e2 ? 'visibility' : 'visibility_off'" :append-icon-cb="() => (e2 = !e2)" :type="e2 ? 'password' : 'text'" counter
                                            ></v-text-field>
                                        </v-flex>
                                        <v-flex xs12>
                                            <small>*campos obrigatório</small>
                                        </v-flex>
                                        <div class="ui error message" v-if="hasErrors">
                                            <p v-for="error in errors">{{ error }}</p>
                                        </div>
                                    </v-layout>
                                </v-container>
                                
                            </v-card-text>
                            <v-toolbar class="orange" dark><v-spacer></v-spacer>
                                <v-toolbar-title>
                                    <v-btn flat dark @click.prevent="register" :class="{ 'loading': isLoading }">
                                        <span>Registre-se </span>
                                        <v-icon dark>save</v-icon>
                                    </v-btn>
                                </v-toolbar-title>
                            </v-toolbar>
                            <div class="ui message">
                                <br>
                                <h6>Já está registado? <router-link to="/login">Conecte-se</router-link></h6>
                                <br>
                            </div>
                        </v-card>
                    </v-flex>
                </v-layout>
            </v-container>
            <p>{{ $data }}</p>
        </template>
    </div>
</div>

</template>

<script>

    import md5 from 'md5'

    export default {
        name: 'register',
        data () {
            return {
                nome: '',
                usuario: '',
                email: '',
                password: '',
                password_confirmation: '',
                empresa:'',
                nivel: 0,
                errors: [],
                usersRef: firebase.database().ref('users'),
                userConts: '',
                isLoading: false,
                rules: {
                required: (value) => !!value || 'exemplo@email.com',
                    email: (value) => {
                        const pattern = /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/
                        return pattern.test(value) || 'E-mail invalido.'
                    }
                },
                e1: true,
                e2: true,
            }
        },
        computed: {
            hasErrors () {
                return this.errors.length > 0
            }
        },
        methods: {
            register () {                
                this.errors = []

                if(this.isFormValid()){
                    this.isLoading = true
                    firebase.auth().createUserWithEmailAndPassword(this.email, this.password).then( user => {              

                    user.updateProfile({
                        displayName: this.usuario,
                        photoURL: "http://www.gravatar.com/avatar/"+md5(user.email)+"?d=identicon"
                    }).then ( () => {

                        //Registro de usuário no bdd
                        this.saveUserToUsersRef(user).then( () => {
                            this.$store.dispatch("setUser", user)
                            this.usersRef.child(user.uid).on('child_added', snap => {              
                                this.userConts.push(snap.val());
                                this.$store.dispatch("setUserCont", userConts);
                            }),
                            //this.addListeners(user)
                            this.$router.push('/') 
                            
                            
                        })

                    }, error => {
                        console.log(error)
                        this.errors.push(error.message)
                        this.isLoading = false
                    })

                    }).catch( error => {
                        console.log(error)
                        this.errors.push(error.message)
                        this.isLoading = false
                    })
                }
                
            },
            saveUserToUsersRef(user){
                return this.usersRef.child(user.uid).set({
                    usuario: user.displayName,
                    avatar: user.photoURL,
                    nome: this.nome,
                    empresa: this.empresa,
                    nivel: this.nivel
                })
            },
            addListeners (user) {
                //let ref = this.getMessageRef()
                return this.usersRef.child(user.uid).on('child_added', snap => {              
                    this.userConts.push(snap.val())
                    this.$store.dispatch("setUserCont", userConts)

                })
            },
            
            isEmpty () {
                if(this.nome.length == 0 || this.email.length == 0 || this.password.length == 0 || this.password_confirmation.length == 0){
                    return true;
                }
                return false;
            },
            passwordValid () {
                if(this.password.length < 6 || this.password_confirmation.length < 6){
                    return false;
                }
                if(this.password !== this.password_confirmation){
                    return false;
                }
                return true;
            },
            isFormValid(){
                if(this.isEmpty()){
                    this.errors.push('Por favor, preencha todos os campos')
                    return false
                }
                if(!this.passwordValid()){
                    this.errors.push('senha incorreta')
                    return false
                }
                return true
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