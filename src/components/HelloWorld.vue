<template>
  <div>
    <axiosuse></axiosuse>
  </div>
</template>

<script>
import axiosuse from "./AxiosUse";
export default {
  name: 'HelloWorld',
  components: {axiosuse},
  data(){
    return{};
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
