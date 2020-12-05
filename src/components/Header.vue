<template>
  <div>
    <b-navbar toggleable="sm" type="light" variant="light">
      <b-navbar-toggle target="nav-text-collapse"></b-navbar-toggle>

      <b-navbar-brand to="/">Clippingz</b-navbar-brand>

      <b-collapse id="nav-text-collapse" is-nav>
        <b-navbar-nav>
          <b-nav-text class="ml-3 text-monospace">Notícias Selecionadas
          <b-icon scale="1" icon="newspaper"></b-icon>
          </b-nav-text>
        </b-navbar-nav>

        <b-navbar-nav class="ml-auto">
          <b-input-group size="sm" prepend="Notícias">
            <b-form-input autocomplete="off" v-model="palavra"></b-form-input>
            <b-input-group-append>
              <b-button size="sm" text="Ok" variant="" @click="pesquistaNewsDB">Pesquisar</b-button>
            </b-input-group-append>
          </b-input-group>
        </b-navbar-nav>

        <b-navbar-nav>
          <b-nav-item class="ml-4" to="/confignews">Acesso Restrito</b-nav-item>
        </b-navbar-nav>
      </b-collapse>
      <b-button v-show="user.data" variant="outline-danger" @click="signOut" class="ml-2" size="sm"><b-icon icon="door-closed"></b-icon></b-button>
    </b-navbar>
  </div>
</template>

<script>
import {mapGetters} from "vuex";
import firebase from "firebase";

export default {
  name: "Header",
  data(){
    return{
      palavra:''
    }
  },
  methods:{
    pesquisaNews(){
      this.$store.commit('resetNewsResult')
      //procura no somente vuex News Lista que é carregada na inicialização do site
      //const newsArr = this.$store.getters.getNewsLista
      //ao invés de buscar no vuex, a busca será no db
      this.$store.dispatch('getNewsDB')
      const newsArr = this.$store.getters.getNewsLista
      //não funciona, pois executa antes de pegar os dados
      for (let i = 0; i < newsArr.length; i++){
        if (newsArr[i].desc.toLowerCase().includes(this.palavra.toLowerCase()) ||
              newsArr[i].content.toLowerCase().includes(this.palavra.toLowerCase()) ||
              newsArr[i].title.toLowerCase().includes(this.palavra.toLowerCase()))
        {
          this.$store.commit('setNewsResult',newsArr[i])
        }
      }
      if (this.$route.name !== 'resultadopesquisa'){
        this.$router.push({name: 'resultadopesquisa'})
      }
    },
    pesquistaNewsDB(){
      this.$store.commit('resetNewsResult')
      //aqui a consulta é direto no banco, mas somente no campo content
      const dataMat = new Date().toLocaleDateString()
      // eslint-disable-next-line no-unused-vars
      const db = firebase.firestore().collection("materias")
          .where('dataMat','==',dataMat)
          .get()
          .then((querySnapshot) =>{
            querySnapshot.forEach((doc) => {
              var strDB = doc.data().content
              if (strDB.toLowerCase().includes(this.palavra.toLowerCase())){
                console.log(doc.data())
                this.$store.commit('setNewsResult',doc.data())
              }
            });
            if (this.$route.name !== 'resultadopesquisa'){
              this.$router.push({name: 'resultadopesquisa'})
            }
          })
          .catch(function(error) {
            console.error("Error getting documents: ", error);
          });
    },
    signOut() {
      firebase
          .auth()
          .signOut()
          .then(() => {
            this.$router.replace({
              name: "lista"
            });
          });
    }
  },
  computed:{
    ...mapGetters({
      user: "user"
    })
  }
}
</script>

<style scoped>

</style>