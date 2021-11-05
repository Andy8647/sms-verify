<template>
  <SMSVerify />
</template>

<script lang="ts">
import { defineComponent } from "@vue/runtime-core";
import SMSVerify from "./components/SMSVerify.vue";

export default defineComponent({
  name: "App",
  components: {
    SMSVerify

  }
})
</script>

<style lang="scss">
body {
  font-size: 16px;
  font-family: Arial, Helvetica, sans-serif;
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  width: 100vw;
  height: 100vh;
}

#app{
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100vw;
  height: 100vh;
}
</style>
