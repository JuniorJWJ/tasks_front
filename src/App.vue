<template>
  <main>
    <router-view />
  </main>
</template>

<script>
export default {
  name: "App",
  components: {},
};
</script>

<style>
main {
  font-family: "Roboto", sans-serif;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  overflow-x: hidden;
  background-color: beige;
}
html,
body {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
</style>
