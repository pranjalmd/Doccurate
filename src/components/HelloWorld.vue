<template>
  <div>
    <p>Hello</p>
    <line-chart></line-chart>
  </div>
</template>

<script>
import LineChart from "./LineChart";
export default {
  name: "HelloWorld",
  components: {
    "line-chart": LineChart
  },
  props: {
    msg: String
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
