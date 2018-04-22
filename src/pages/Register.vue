<template>
<div>
	<div class="limiter">
		<div class="container-login100">


			<div class="wrap-login100" style="text-align: center; position: relative;">
				<div style="width: 80%; display: block; position: absolute; top: 80px; z-index: 400; margin-top: -20px;">
					<img src="images/logo.png" alt="" srcset="" style="width: 400px; max-width: 100%">
				</div>
				<br>

				<div class="login100-pic js-tilt" data-tilt>
					<img src="images/register.png" alt="IMG">
				</div>

				<form class="login100-form validate-form" @submit.prevent="register">

          
					<span class="login100-form-title">
						Register staff
					</span>


              <div class="wrap-input100 validate-input">
                        <input v-model="name" class="input100" type="text" name="name" placeholder="Name">
                        <span class="focus-input100"></span>
                        <span class="symbol-input100">
                            <i class="fa fa-user-circle" aria-hidden="true"></i>
                        </span>
                    </div>

                    <div class="wrap-input100 validate-input" >
                        <input v-model="role" class="input100" type="text" name="role" placeholder="Role">
                        <span class="focus-input100"></span>
                        <span class="symbol-input100">
                            <i class="fa fa-id-card-o " aria-hidden="true"></i>
                        </span>
                    </div>

					<div class="container-login100-form-btn">
						<button class="login100-form-btn">
							Register
						</button>
					</div>

					<!-- <div class="text-center p-t-136">
						<a class="txt2" href="#">
							Contact
							<i class="fa fa-long-arrow-right m-l-5" aria-hidden="true"></i>
						</a>
					</div> -->
				</form>
			</div>
		</div>
	</div>


  
<spinner :visible="isSpinnerVisible"/>

  <!-- <div>
    <div class="container mt-5">
      <div class="row justify-content-sm-center">
        <div class="col-md-6 col-md-offset-3">
          <h1 class="mt-5 mb-4">Authorization</h1>
          <tabs>
            <tab :is-active="true" title="Registration">
              <form @submit.prevent="register">
                <div class="form-group">
                  <label class="control-label">Name:</label>
                  <input v-model="name" type="text" class="form-control" placeholder="Enter name" maxlength="260" required>
                  <select v-model="role">
                    <option value="producer">Producer</option>
                    <option value="certifier">Certifier</option>
                    <option value="trader">Trader</option>
                    <option value="customer">Customer</option>
                  </select>
                </div>
                <button type="submit" class="btn btn-lg btn-block btn-primary">Register</button>
              </form>
            </tab>
            <tab title="Log In">
              <form @submit.prevent="login">
                <div class="form-group">
                  <label class="control-label">Secret key:</label>
                  <input v-model="secretKey" type="text" class="form-control" placeholder="Enter secret key" required>
                </div>
                <button type="submit" class="btn btn-lg btn-block btn-primary">Log In</button>
              </form>
            </tab>
          </tabs>
        </div>
      </div>
    </div> -->

    <modal :visible="isModalVisible" title="Successful registration" action-btn="Send to Email" @close="closeModal" @submit="proceed">
      <div class="alert alert-warning" role="alert">Save the secret key in a safe place. You will need it to log in to the demo next time.</div>
      <div class="form-group">
        <label>Secret key:</label>
        <div><code>{{ keyPair.secretKey }}</code></div>
      </div>
    </modal>


    
  </div> 
</template>

<script>
import Spinner from "../components/Spinner.vue";
import Tab from "../components/Tab.vue";
import Tabs from "../components/Tabs.vue";
import Modal from "../components/Modal.vue";

module.exports = {
  components: {
    Tab,
    Tabs,
    Modal,
    Spinner
  },
  data() {
    return {
      keyPair: {},
      isModalVisible: false,
      isSpinnerVisible: false
    };
  },
  methods: {
    login() {
      this.isSpinnerVisible = true;

      this.$store.commit("login", {
        publicKey: this.secretKey.substr(64),
        secretKey: this.secretKey
      });

      this.$nextTick(function() {
        this.$router.push({ name: "dashboard" });
      });
    },

    async register() {
      this.isSpinnerVisible = true;

      try {
        const keyPair = await this.$blockchain.createUser(this.name, this.role);
        this.name = "";
        this.role = "";
        this.keyPair = keyPair;
        this.isSpinnerVisible = false;
        this.isModalVisible = true;
      } catch (error) {
        this.isSpinnerVisible = false;
        this.$notify("error", error.toString());
      }
    },

    closeModal() {
      this.isModalVisible = false;
    },

    proceed() {
      this.isModalVisible = false;

      this.$store.commit("login", this.keyPair);

      this.$nextTick(function() {
        this.$router.push({ name: "dashboard" });
      });
    }
  }
};
</script>
