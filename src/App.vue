<template>
  <div>
 
    <router-view/>

   </div>



</template>


<script>
const Navbar = require("./components/Navbar.vue");

module.exports = {
  components: {
    Navbar
  },
  computed: {
    background() {
      return require("./bgs/" + this.id + ".jpg");
    }
  }
};
</script>


<style>
input:invalid {
  box-shadow: none;
}

code,
.break-word {
  word-break: break-all;
  word-wrap: break-word;
}
</style>
