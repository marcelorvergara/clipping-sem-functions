<template>
  <b-container class="container-fluid mt-5">
    <b-row class="mt-2" cols-sm="3">
      <b-col></b-col>
      <b-col style="min-width: 380px">
        <b-card no-body
                header="Página de Login do Acesso Restrito"
                align="left"
                bg-variant="">
          <div class="card-body">
            <b-alert v-if="sucesso" show="5" variant="success" dismissible>{{sucesso}}</b-alert >
            <b-alert v-if="error" show="10" variant="danger" dismissible>{{error}}</b-alert >
            <form action="#" @submit.prevent="logar">

              <b-input-group class="" size="">
                <b-input-group-append>
                  <b-input-group-text>
                    <b-icon icon="at" />
                  </b-input-group-text>
                </b-input-group-append>
                  <b-form-input
                      id="email"
                      type="email"
                      class="form-control"
                      name="email"
                      value
                      required
                      autofocus
                      v-model="form.email"
                      placeholder="Insira o login"
                  />
              </b-input-group>

              <b-input-group class=" mt-3 mb-4" size="">
                <b-input-group-append>
                  <b-input-group-text>
                    <b-icon icon="key" />
                  </b-input-group-text>
                </b-input-group-append>
                  <b-form-input
                      id="senha"
                      type="password"
                      class="form-control"
                      name="senha"
                      v-model="form.senha"
                      placeholder="Insira a senha"
                  />
                </b-input-group>
              <b-form-group class="mb-0 text-right">
                <b-button size="sm" @click="$router.go(-1)" variant="outline-dark" class="ml-2">Voltar</b-button>
                <b-button size="sm" @click="restSenha" variant="outline-dark"  class="ml-2">
                  Reset Senha
                  <b-spinner v-show="loadingRS" small label="Carregando..."></b-spinner>
                </b-button>
                <b-button size="sm" type="submit" variant="outline-dark" class="ml-2">
                  Login
                  <b-spinner v-show="loading" small label="Carregando..."></b-spinner>
                </b-button>
              </b-form-group>
            </form>
          </div>
        </b-card>
      </b-col>
      <b-col></b-col>
    </b-row>
  </b-container>
</template>

<script>
import firebase from "firebase";

export default {
  data() {
    return {
      loadingRS:false,
      loading: false,
      form: {
        email: "",
        senha: ""
      },
      error: null,
      sucesso:null
    };
  },
  methods: {
    restSenha(){
      this.error = ''
      this.sucesso = ''
      this.loadingRS = true;
      var auth = firebase.auth();

      auth.sendPasswordResetEmail(this.form.email).then(() => {
        this.sucesso = `E-mail enviado para ${this.form.email} para procedimento de reset de senha!`
        this.loadingRS = false;
      }).catch((error) => {
        this.loadingRS = false;
        console.log(error.message)
        if (error.message === 'There is no user record corresponding to this identifier. The user may have been deleted.'){
          this.error = 'Email ou login não encontrado!'
        } else if (error.message === 'The email address is badly formatted.'){
          this.error = 'Email inválido!'
        }
      });
    },
    logar() {
      this.error = ''
      this.loading = true
      firebase.auth().signInWithEmailAndPassword(this.form.email, this.form.senha)
          .then(() => {
            this.loading = false;
            this.$router.replace({ name: "confignews" });
          })
          .catch((error) => {
            this.loadingRS = false;
            console.log(error.message)
            if (error.message === 'There is no user record corresponding to this identifier. The user may have been deleted.'){
              this.error = 'Email ou login não encontrado!'
            } else if (error.message === 'The email address is badly formatted.'){
              this.error = 'Email inválido!'
            } else if (error.message === 'The password is invalid or the user does not have a password.'){
              this.error = 'Email e/ou senha inválidos!'
            }
            this.loading = false;
          });
    }
  }
};
</script>

<style scoped>

</style>