<template>
  <div>
    <button data-toggle="modal" data-target="#exampleModal" @click="toggleModal()">Télécharger l'application</button>
    <!-- Modal Log-->
    <div v-if="isModalActive"
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      role="dialog"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog" role="document">
        <div class="modal-content">
          <!-- On est pas connecté -->
          <template v-if="!isAuthenticated">
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Avant de télécharger votre Application</h5>

              <button @click="toggleModal()"
                type="button"
                class="close exit-js"
                data-dismiss="modal"
                aria-label="Close"
              >
                <span style="font-size: 40px;font-weight: 300;" aria-hidden="true">&times;</span>
              </button>
            </div>

            <div class="modal-body">
              <h4>Merci d'entrer le mot de passe que vous avez reçu par mail.</h4>
              <form>
                <div class="form-group d-flex">
                  <label class="col-4" for="exampleInputPassword1">Mot de passe</label>
                  <input ref="password" type="password" class="form-control col-8" id="exampleInputPassword1" v-model="activePassword" placeholder="**********">
                </div>
                <div class="message-wrapper">
                  <span>
                    <template v-if="success === true">Mot de passe correct !</template>
                    <template v-else-if="success === false">Mot de passe incorrect, réessayez !</template>
                  </span>
                  <img class="spinner" v-if="isLoading" :src="require('@/assets/spinner.gif')" alt="spinner">
                </div>
                
                <button type="submit" class="btn-submit-modal btn btn-primary js-send" @click="validate()">Valider</button>
              </form>
            </div>
            
          </template>
          <!-- On est connecté -->
          <template v-else>
            <div class="modal-header">
              <h5 class="modal-title" id="exampleModalLabel">Choisissez votre Version</h5>
              <button @click="toggleModal()"
                type="button"
                class="close exit-js"
                data-dismiss="modal"
                aria-label="Close"
              >
                <span style="font-size: 40px;font-weight: 300;" aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <Back />
            </div>
          </template>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Back from "@/components/Back";

export default {
  name: 'Modal',
  components: {
    Back,
  },
  data() {
    return {
      activePassword: undefined,
      isModalActive: false,
      isLoading: false,
      success: undefined
    };
  },
  computed: {
    password () {
      return this.$store.state.password
    },
    isAuthenticated () {
      return this.$store.state.isAuthenticated
    }
  },
  methods: {
    toggleModal: function () {
      this.isModalActive = !this.isModalActive
      this.success = undefined
      this.isLoading = false
      if (this.isModalActive) {
        setTimeout(() => {
          this.$nextTick(() => this.$refs.password.focus())
        }, 500);
      }
    },
    validate: function () {
      this.isLoading = true
      var apiUrl = "https://services.cook-and-connect.aioa.fr/check-password.php";
      var hash = "$2b$06$75BmGlZVqfTdqM39A7.1OuzdmfJqCaG5hfkeO2760xfLMuVYLupjW";
      var datas = {
        hash: hash,
        password: this.activePassword
      }
    
      // Requête qui vérifie le mot de passe et qui fais les petites animations si le mdp est bon ou pas
      $.ajax({
        url: apiUrl,
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        method: 'POST',
        dataType: 'json',
        data: datas,
      }).done((response) => {
        this.isLoading = false
        if (response.ok) {
          this.success = true
          this.$store.commit('setPassword', this.activePassword)
        } else {
          this.success = false
        }
      });
    }
  }
};
</script>

<style scoped lang="less">
.modal {
  top: 25%;
}
.form-group {
  justify-content: space-around;
  margin: 2rem 0rem;
  align-items: center;
}
.modal-title {
  font-size: 34px;
  color: rgba(black, 0.8);
  padding: 0.4rem 1.5rem;
}
.btn-submit-modal {
  background-color:#c4ad99;
  border-color: #c4ad99;
  box-shadow: 4px 2px 4px #d1d1d1;
}
.btn-submit-modal:hover {
  box-shadow: 5px 5px 4px #d1d1d1;
}
.modal-body {
  > h4 {
    font-weight: normal;
    font-size: 16px;
    color: black;
    font-weight: 300;
  }
}
button.close {
  box-shadow: none;
  -webkit-box-shadow: none;
}
.modal-body-after {
  display: none;
}
.list-group {
  width: 100%;
}
.list-group-item.active {
  background-color: #c4ad99;
  border-color: #c4ad99;
}
.card-header {
  padding: 0;
}
.version-link {
  width: 100%;
  margin: 0;
  padding: 1rem;
  text-align: left;
  color: #c4ad99;
  transition: all 0.5s ease;
  text-decoration: none;
}
.version-link:hover {
  background-color: rgba(#c4ad99, 0.5);
  color: #fff;
  transition: all 0.5s ease;
}
.card-body {
  display: flex;
  justify-content: space-between ;
  align-items: center;
  > p {
    margin-bottom: 0;
    font-size: 16px;
    margin-right: 3rem;
    text-align: left;
  }
}
.modal-title-after {
  display: none;
  font-size: 34px;
  color: rgba(black, 0.8);
  padding: 0.4rem 1.5rem;
}
.fa-download {
  content: '\f019';
}

.message-wrapper {
  text-align: center;
  margin: 1.5rem  0rem;

  .spinner {
    width: 30px;
    height: auto;
  }
}
</style>
