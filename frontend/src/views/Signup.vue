<template>
    <div class="sign">
        <div id="nav">
            <router-link to="/">Retourner à la page d'accueil</router-link>
        </div>
        <div class="signup">
          <img src="../assets/logo-white-resize.png" alt="logo groupomania blanc" class="logo">
          <img src="../assets/logo-white-rs.png" alt="logo groupomania blanc" class="logoResp">
          <MsgComponents msg="Inscrivez-vous" />
          <div class="form-container">
            <form @submit.prevent="submitForm" autocomplete="off">
                <div class="name-container">
                    <label for="name">Nom</label>
                    <input type="text" v-model="form.name" name="name" id="name" autocomplete="off" aria-required="true" aria-label="Renseignez votre nom" required>
                    <span  v-if="!nameIsValid" class="error">Merci de renseigner votre nom</span>
                </div>
                <div class="firstname-container">
                    <label for="firstname">Prénom</label>
                    <input type="text" v-model="form.firstname" name="firstname" id="firstname" autocomplete="off" aria-required="true" aria-label="Renseignez votre prenom" required>
                    <span v-if="!firstnameIsValid" class="error">Merci de renseigner votre prénom</span>
                </div>
                <div class="email-container">
                    <label for="email">E-mail</label>
                    <input type="email" v-model="form.email" name="email" id="email" autocomplete="off" aria-required="true" aria-label="Renseignez votre adresse mail" required>
                    <span v-if="!emailIsValid" class="error">Merci de renseigner l'e-mail</span>
                </div>
                <div class="password-container">
                    <label for="password">Mot de passe</label>
                    <input type="password" v-model="form.password" name="password" id="password" autocomplete="off" aria-required="true" aria-label="Créez votre mot de passe" required>
                    <p id="progress-bar" class="progressRed" v-if="(form.password.length >= 1) && (form.password.length < 8)"></p>
                    <p id="progress-bar" class="progressBlue" v-if="(form.password.length >= 8) && (form.password.length < 12)"></p>
                    <p id="progress-bar" class="progressGreen" v-if="form.password.length >= 12"></p>
                    <span v-if="!passwordIsValid" class="error error-password">Le mot de passe doit contenir au minimum 
                      <span v-if="form.password.length < 8">8 caractères</span>
                      <span v-if="!((/.*[A-Z]/).test(this.form.password))"> une majuscule</span> 
                      <span v-if="!((/.*\d/).test(this.form.password))"> un chiffre</span> 
                      <span v-if="!((/.*[+\*-\/?!.$]/).test(this.form.password))"> un caractère spécial</span>
                    </span>
                </div>
                <div class="confirm-container">
                    <label for="confirm">Confirmer mot de passe</label>
                    <input type="password" v-model="form.confirm" name="confirm" id="confirm" autocomplete="off" aria-required="true" aria-label="confirmer votre mot de passe" required>
                    <span v-if="form.confirm != form.password">Les mots de passe ne correspondent pas</span>
                </div>
                <button aria-label="valider">Créer mon compte</button>
            </form>
          </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";
import MsgComponents from "@/components/MsgComponents.vue"

export default {
    name: "Signup",
    components: {
        MsgComponents
    },
    data() {
        return {
            form: {
                name: null,
                firstname: null,
                email: null,
                password: "",
                confirm: "",
            }     
        }
    },
    computed: {
        nameIsValid() { //on défini ici car les propriétés calculées vont detecter les changements des inputs
            return !!this.form.name; //le !! test si this.form.name contient quelque chose. Renvoi true si il contient quelque chose
        },
        firstnameIsValid() {
            return !!this.form.firstname;
        },
        emailIsValid() {
          return !!(/^([a-zA-Z0-9._-]+@[a-zA-Z0-9._-]{2,}\.[a-z]{2,4})$/.test(this.form.email))
        },
        passwordIsValid() {
          return !!(/^(?=.*?[A-Z])(?=(.*[a-z]){1,})(?=(.*[\d]){1,})(?=(.*[\W]){1,})(?!.*\s).{8,}$/.test(this.form.password))
        },
        formIsValid() {
          return this.nameIsValid && this.firstnameIsValid && this.emailIsValid && this.passwordIsValid && !(this.form.confirm != this.passwordIsValid);
        },
    },
    methods: {
        submitForm() {

            const formIsValid = this.nameIsValid && this.firstnameIsValid && this.emailIsValid && this.passwordIsValid && (this.form.confirm == this.form.password);
            
            if (formIsValid) {
              const self = this;
              axios.post("http://localhost:3000/api/user/signup", this.form)
                .then(function(response) {
                alert("Vous venez de créer votre compte. Désormais, vous pouvez accéder au site en vous connectant.")
                self.$router.push('/login');
                console.log(response);
                })
                .catch(function(error) {
                  alert("Cette adresse mail est déjà utilisée")
                  self.form.email = null;
                 console.log(error);
                })
            } else {
              console.log("Invalid form");
            }
        },
    }
}
</script>

<style lang="scss" scoped>
a {
  text-decoration: none;
  color: #000;

  &:hover {
    color: #fc2e06;
  }
}
.logoResp {
  display: none;
}
.logo {
  margin-top: -20px;
}
.form-container {
    display: flex;
    margin-top: 20px;
}
form {
  margin: auto;
  width: 50%;
  max-width: 600px;
  background: #ffffff;
  display: grid;
  grid-template-rows: repeat(4, 104px);
  padding: 60px 45px 30px;
  border-radius: 16px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.05);
  position: relative;
}
.signup {
  margin-top: 20px;
}
div {
  transition: 0.2s;
}
label {
  letter-spacing: 1px;
}
input[type="text"],
input[type="password"],
input[type="email"] {
  margin: 7px 0 7px;
  border: 1px solid transparent;
  background: rgb(235, 244, 242);
  height: 36px;
  width: 100%;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
  border-radius: 9px;
  font-size: 1.1rem;
  padding-left: 10px;
  transition: 0.2s;
}
span {
  margin-bottom: 20px;
  font-size: 0.8rem;
  color: red;
  visibility: visible;
  opacity: 1;
}
p {
  width: 33%;
  height: 6px;
  margin: -2px 0 2px;
  border-radius: 50px;
  visibility: hidden;
  transition: 0.3s;
}
button {
  border: none;
  background: linear-gradient(65deg, #f89e8c, #fc2e06);
  margin: 12px auto 0;
  padding: 10px;
  border-radius: 150px;
  font-size: 1.2rem;
  box-shadow: 0 1px 10px rgba(0, 0, 0, 0.2);
  cursor: pointer;
  transition: 0.3s ease;

  &:hover {
      filter: brightness(105%);
      transform: translateX(2px);
  }
}
.progressRed {
  background: red;
  width: 33%;
  visibility: visible;
}
.progressBlue {
  background: #16d9e7;
  width: 67%;
  visibility: visible;
}
.progressGreen {
  width: 100%;
  background: #5ef9a4;
  visibility: visible;
}
@media all and (max-width : 1155px) {
  .confirm-container {
    margin-top: 30px;
  }
}
@media all and (max-width : 760px) {
  .form-container {
    width: 100%;
  }
}
@media all and (max-width : 634px) {
  .confirm-container {
    margin-top: 50px;
  }
}
@media all and (max-width : 536px) {
  .confirm-container {
    margin-top: 30px;
  }
  form {
    margin: 0;
    width: 100%;
  }
}
@media all and (max-width : 415px) {
  .confirm-container {
    margin-top: 50px;
  }
}
@media all and (max-width : 380px) {
  .logo {
    display: none;
  }
  .logoResp {
    display: initial;
    width: 200px;
  }
}
@media all and (max-width: 313px) {
  .confirm-container {
    margin-top: 80px;
  }
}
@media all and (max-width : 295px) {
  .email-container {
    margin-top: 25px;
  }
  .password-container {
    margin-top: 30px;
  }
  .confirm-container {
    margin-top: 110px;
  }

}
</style>
