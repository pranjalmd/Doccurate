<template>
  <div >
    <div class="d-flex" id="wrapper" style="margin-left:2%; width:15%">
      <!-- Sidebar -->
      <div style="height:300px;">
          <div class=" sidebar-heading">Patient Information</div>
          <div>
            <a href="#" class="list-group-item list-group-item-action bg-light">Name</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Age</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Height</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Weight</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">info1</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">info2</a>
          </div>
          <div style="margin-top: 25%;" class="sidebar-heading">Filter Collection </div>
          <div>
            <button class="list-group-item list-group-item-action bg-light" type="button">disease1</button>
            <button class="list-group-item list-group-item-action bg-light" type="button">disease2</button>
            <button class="list-group-item list-group-item-action bg-light" type="button">disease3</button>
            <button class="list-group-item list-group-item-action bg-light" type="button">disease4</button>
          </div>
      </div>
      <!-- /#sidebar-wrapper -->
    </div>

  <div style="margin-left: 5%" class = "column" id="wrapper">
    <div>   <line-chart></line-chart> </div>
    <div>   <line-chart></line-chart> </div>
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
