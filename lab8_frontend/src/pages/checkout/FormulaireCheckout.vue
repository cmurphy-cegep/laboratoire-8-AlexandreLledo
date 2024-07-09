<template>
  <div class="boxed-left">
    <form @submit.prevent="soumettreInfos">
      <div>
        <h2>Paiement</h2>
        <div :class="{ invalide: !nomPaiementValide }" class="form-control">
          <label for="nom-paiement">Nom sur la carte de crédit: </label>
          <!-- L'événement blur survient lorsqu'on perd le focus de ce champ -->
          <!-- Le modificateur .trim ajouté à v-model supprime les espaces au début et à la fin de la chaîne entrée -->
          <input id="nom-paiement" v-model.trim="nomPaiement" name="nom-paiement" type="text"
                 @blur="validerNomPaiement"/>
          <span v-if="!nomPaiementValide">Veuillez entrer un nom !</span>
        </div>
        <div :class="{ invalide: !carteCreditValide }" class="form-control">
          <label for="cartecredit">Numéro de carte de crédit: </label>
          <input id="cartecredit" v-model.trim="carteCredit" name="cartecredit" type="text"/>
          <span v-if="!carteCreditValide">Veuillez entrer un numéro de carte de crédit sous la forme 4555 5555
                        5555 5555</span>
        </div>
        <div :class="{ invalide: !dateExpValide }" class="form-control">
          <label for="dateexp">Année et mois d'expiration (AAAA/MM): </label>
          <input id="dateexp" v-model.trim="dateExp" name="dateexp" type="text"/>
          <span v-if="!dateExpValide">Veuillez entrer une date d'expiration, p.ex. 2024/05</span>
        </div>
      </div>
      <h2>Expédition</h2>
      <div>
        <h4>Mode d'expédition</h4>
        <div class="form-control">
          <input id="expedition-postes-canada" v-model="modeExpedition" name="expedition" type="radio"
                 value="postescanada"/>
          <label for="expedition-postes-canada">Postes Canada</label>
          <input id="expedition-purolator" v-model="modeExpedition" name="expedition" type="radio"
                 value="purolator"/>
          <label for="expedition-purolator">Purolator</label>
          <input id="expedition-fedex" v-model="modeExpedition" name="expedition" type="radio" value="fedex"/>
          <label for="expedition-fedex">FedEx</label>
        </div>
      </div>
      <div>
        <h4>Adresse</h4>
        <div :class="{ invalide: !nomExpeditionValide }" class="form-control">
          <label for="nom-expedition">Nom: </label>
          <input id="nom-expedition" v-model.trim="nomExpedition" name="nom-expedition" type="text"/>
          <span v-if="!nomExpeditionValide">Veuillez entrer un nom</span>
        </div>
        <div :class="{ invalide: !adresseValide }" class="form-control">
          <label for="adresse-expedition">Adresse: </label>
          <input id="adresse-expedition" v-model.trim="adresse" name="adresse-expedition" type="text"/>
          <span v-if="!adresseValide">Veuillez entrer une adresse</span>
        </div>
        <div :class="{ invalide: !villeValide }" class="form-control">
          <label for="ville-expedition">Ville: </label>
          <input id="ville-expedition" v-model.trim="ville" name="ville-expedition" type="text"/>
          <span v-if="!villeValide">Veuillez entrer une ville</span>
        </div>
        <div :class="{ invalide: !provinceValide }" class="form-control">
          <label for="province">Province: </label>
          <select id="province" v-model="province" name="province">
            <option v-for="province in listeProvinces" :value="province">{{ province }}</option>
          </select>
          <span v-if="!provinceValide">Veuillez choisir une province</span>
        </div>
        <div :class="{ invalide: !codePostalValide }" class="form-control">
          <label for="codepostal-expedition">Code postal: </label>
          <input id="codepostal-expedition" v-model.trim="codePostal" name="codepostal-expedition" type="text"/>
          <span v-if="!codePostalValide">Veuillez entrer un code postal valide</span>
        </div>
      </div>
      <button>Passer la commande</button>
    </form>
  </div>
</template>

<script>
import session from '../../session';

export default {
  inject: ['cart'],
  data() {
    return {
      nomPaiement: '',
      nomPaiementValide: true,
      carteCredit: '',
      carteCreditValide: true,
      dateExp: '',
      dateExpValide: true,
      modeExpedition: 'postescanada',
      nomExpedition: '',
      nomExpeditionValide: true,
      adresse: '',
      adresseValide: true,
      ville: '',
      villeValide: true,
      province: '',
      provinceValide: true,
      listeProvinces:
          [
            'Yukon',
            'Territoires du Nord-Ouest',
            'Nunavut',
            'Colombie-Britannique',
            'Alberta',
            'Saskatchewan',
            'Manitoba',
            'Ontario',
            'Québec',
            'Nouveau-Brunswick',
            'Nouvelle-Écosse',
            'Île-du-Prince-Édouard',
            'Terre-Neuve et Labrador'
          ],
      codePostal: '',
      codePostalValide: true
    }
  },
  methods: {
    soumettreInfos() {
      console.log('nomPaiement: ', this.nomPaiement);
      console.log('carteCredit: ', this.carteCredit);
      console.log('dateExp: ', this.dateExp);
      console.log('modeExpedition: ', this.modeExpedition);
      console.log('nomExpedition: ', this.nomExpedition);
      console.log('adresse: ', this.adresse);
      console.log('ville: ', this.ville);
      console.log('province: ', this.province);
      console.log('nomPaiement: ', this.nomPaiement);
      console.log('codePostal: ', this.codePostal);

      this.validerNomPaiement();
      this.validerCarteCredit();
      this.validerDateExp();
      this.validerNomExpedition();
      this.validerAdresse();
      this.validerVille();
      this.validerProvince();
      this.validerCodePostal();

      if (this.nomPaiementValide && this.carteCreditValide && this.dateExpValide && this.nomExpeditionValide
          && this.adresseValide && this.villeValide && this.provinceValide && this.codePostalValide) {
        this.envoyerCommande();
      } else {
        alert("Veuillez corriger les informations erronées");
      }
    },
    reinitialiserFormulaire() {
      this.nomPaiement = '';
      this.nomPaiementValide = true;
      this.carteCredit = '';
      this.carteCreditValide = true;
      this.dateExp = '';
      this.dateExpValide = true;
      this.modeExpedition = 'postescanada';
      this.nomExpedition = '';
      this.nomExpeditionValide = true;
      this.adresse = '';
      this.adresseValide = true;
      this.ville = '';
      this.villeValide = true;
      this.province = '';
      this.provinceValide = true;
    },
    envoyerCommande() {
      // ** Exercice 2.2 **
      // Modifier cette méthode afin de soumettre une requête HTTP POST
      // authentifiée au back-end avec le compte de l'utilisateur ou
      // utilisatrice présentement connecté(e).

      const commande = {
        userId: session.user, // ** Exercice 2.2 : à remplacer par l'utilisateur/utilisatrice connecté(e) **
        paiement: {
          nomCarteCredit: this.nomPaiement,
          noCarteCredit: this.carteCredit,
          expCarteCredit: this.dateExp
        },
        modeExp: this.modeExpedition,
        adresse: {
          nom: this.nomExpedition,
          adresse: this.adresse,
          ville: this.ville,
          province: this.province,
          codePostal: this.codePostal
        }
      };

      fetch("/api/orders", {
        method: "POST",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(commande)
      }).then((response) => {
        if (response.ok) {
          alert("La commande a bien été reçue, merci d'avoir acheté au Panier Vert !");
          this.cart.fetchCart();
          this.reinitialiserFormulaire();
        } else {
          throw new Error(`Erreur ${response.status}`);
        }
      }).catch((error) => {
        console.error("Erreur", error);
      });
    },
    validerNomPaiement() {
      if (this.nomPaiement === '') {
        this.nomPaiementValide = false;
      } else {
        this.nomPaiementValide = true;
      }
    },
    validerCarteCredit() {
      const regex = /^4[0-9]{3}(?: [0-9]{4}){3}$/;
      if (this.carteCredit && regex.test(this.carteCredit)) {
        this.carteCreditValide = true;
      } else {
        this.carteCreditValide = false;
      }
    },
    // Informations utiles :
    // regex pour valider le mois et année (AAAA/MM) : /^[0-9]{4}\/[0-1][0-9]$/
    // (permet d'entrer des mois plus grands que 12 mais bon ...)
    //
    // regex pour valider le code postal : /^[ABCEGHJKLMNPRSTVXY][0-9][ABCEGHJKLMNPRSTVWXYZ] ?[0-9][ABCEGHJKLMNPRSTVWXYZ][0-9]$/
    validerDateExp() {
      const regex = /^[0-9]{4}\/[0-1][0-9]$/;
      if (this.dateExp && regex.test(this.dateExp)) {
        this.dateExpValide = true;
      } else {
        this.dateExpValide = false;
      }
    },
    validerNomExpedition() {
      if (this.nomExpedition !== '') {
        this.nomExpeditionValide = true;
      } else {
        this.nomExpeditionValide = false;
      }
    },
    validerAdresse() {
      if (this.adresse !== '') {
        this.adresseValide = true;
      } else {
        this.adresseValide = false;
      }
    },
    validerVille() {
      if (this.ville !== '') {
        this.villeValide = true;
      } else {
        this.villeValide = false;
      }
    },
    validerProvince() {
      if (this.province !== '') {
        this.provinceValide = true;
      } else {
        this.provinceValide = false;
      }
    },
    validerCodePostal() {
      const regex = /^[ABCEGHJKLMNPRSTVXY][0-9][ABCEGHJKLMNPRSTVWXYZ] ?[0-9][ABCEGHJKLMNPRSTVWXYZ][0-9]$/;
      if (this.codePostal && regex.test(this.codePostal)) {
        this.codePostalValide = true;
      } else {
        this.codePostalValide = false;
      }
    }
  },
  watch: {
    carteCredit(nouvCarteCredit) {
      this.validerCarteCredit();
    },
    dateExp(nouvDateExp) {
      this.validerDateExp();
    },
    nomExpedition(nouvNomExpedition) {
      this.validerNomExpedition();
    },
    adresse(nouvAdresse) {
      this.validerAdresse();
    },
    ville(nouvVille) {
      this.validerVille();
    },
    province(nouvProvince) {
      this.validerProvince();
    },
    codePostal(nouvCodePostal) {
      this.validerCodePostal();
    }
  }
}
</script>

<style scoped>
.form-control.invalide input,
.form-control.invalide select {
  border-color: red;
}

.form-control.invalide label {
  color: red;
}

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
