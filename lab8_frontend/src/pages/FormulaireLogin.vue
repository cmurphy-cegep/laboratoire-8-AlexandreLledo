<!--
    ** Exercice 2.1 : compléter le formulaire d'authentification **
    On doit avoir un simple formulaire avec des champs pour l'identifiant utilisateur et le mot de passe.
    Assurez-vous que le mot de passe n'est pas affiché à l'écran quand on l'entre !
    La logique associée au formulaire doit appeler la méthode login(...) du module session.js afin
    de passer les informations de connexion. Une fois authentifié, on peut faire une redirection vers
    la page d'accueil (chemin "/") grâce au routeur de Vue.js.

    Référez-vous au composant FormulaireLogin.vue de l'exemple du cours 19 pour plus de détails.
-->
<template>
  <div class="boxed-left">
    <form @submit.prevent="login">
      <div><label for="nomCompte">Compte utilisateur: </label><input id="nomCompte" v-model="nomCompte"/></div>
      <div><label for="motDePasse">Mot de passe: </label><input id="motDePasse" v-model="motDePasse"
                                                                type="password"/></div>
      <button>Se connecter</button>
    </form>
  </div>
</template>

<script>
import session from '../session';

export default {
  data: function () {
    return {
      nomCompte: '',
      motDePasse: ''
    };
  },
  methods: {
    login() {
      session.login(this.nomCompte, this.motDePasse).then(user => {
        alert("Bienvenue, " + user.userFullName + (user.isAdmin ? ".\nVous êtes administrateur." : "."));
        this.$router.push('/');
      }).catch(authError => {
        alert(authError.message);
      });
    }
  }
}
</script>

<style scoped>
form * {
  margin: 0.3rem;
}

.boxed-left {
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.26);
  margin: 1rem auto;
  border-radius: 10px;
  padding: 1rem;
  text-align: left;
  width: 90%;
  max-width: 80rem;
}
</style>
