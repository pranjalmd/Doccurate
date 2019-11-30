<template>
  <div class="d-flex" id="wrapper" style="margin-left:2%;">
    <div style="height:300px; width:11%;">
      <div style = "background-color: lightgray;text-align: left" class="sidebar-heading">
        <b style="margin-left:2%;">Patient Information</b>
      </div>
      <div style = "background-color: lightgray; overflow:hidden;" id = "patientinfo">
      </div>
      <div style="margin-top: 20%;" class="sidebar-heading">
        <b>Filter Collection</b>
      </div>
      <div >
        <button
          style="color:blue; cursor: pointer; border-radius: 40px; background-color: lightgray; "
          class="list-group-item list-group-item-action"
          type="button"
          @click = "tempfn"
        >
        <b>disease1</b>
        </button>
        <button
          style="color:red; cursor: pointer; border-radius: 40px; background-color: lightgray; margin-top:1%;"
          class="list-group-item list-group-item-action"
          type="button"
        >
          <b>disease2</b>
        </button>
        <button
          style="color:green; cursor: pointer; border-radius: 40px; background-color: lightgray; margin-top:1%;"
          class="list-group-item list-group-item-action"
          type="button"
        >
        <b>disease3</b>
        </button>
        <button
          style="color:brown; cursor: pointer; border-radius: 40px; background-color: lightgray; margin-top:1%;"
          class="list-group-item list-group-item-action"
          type="button"
        >
          <b>disease4</b>
        </button>
      </div>
    </div>

    <div style="margin-left:2%">
      <line-chart></line-chart>
    </div>

    <div style="margin-left:2%; width: 35%;" id="textdetails">
    </div>
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
  },
  methods: {
    tempfn: function(){
      alert("gqacdjgaef");
    }
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
