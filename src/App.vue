<template>
  <Suspense>
    <template #default>
      <Home />
    </template>
    <template #fallback>
      <SplashScreen />
    </template>
  </Suspense>
</template>

<script>
import SplashScreen from "@/components/SplashScreen";
// import MyHome from "@/components/MyHome"
import { defineAsyncComponent } from "vue";
export default {
  components: {
    SplashScreen,
    Home: defineAsyncComponent(() => {
      return new Promise((resolve) => {
        setTimeout(() => {
          resolve(require("./components/MyHome"));
        }, 2500);
      });
    }),
  },
};
</script>
<style>
html,
body,
.app {
  min-height: 100vh;
  margin: 0;
  font-family: Arial, Helvetica, sans-serif;
}
* {
  --brand-green: #04b500;
  --brand-blue: #0689b0;
}
</style>
