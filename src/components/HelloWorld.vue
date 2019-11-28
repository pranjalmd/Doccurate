<template>
  <div class="d-flex" id="wrapper" style="margin-left:2%;">
    <div style="height:300px; width:10%;">
      <div class="sidebar-heading">
        <b>Patient Information</b>
      </div>
      <div>
        <h6 class="list-group-item list-group-item-action bg-light">
          <b>Name:</b>
          <br />
          <br />
          <b>Age:</b>
          <br />
          <br />
          <b>Height:</b>
          <br />
          <br />
          <b>Weight:</b>
          <br />
          <br />
          <b>Gender:</b>
        </h6>
        <!-- <a href="#" class="list-group-item list-group-item-action bg-light">Name</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Age</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Height</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">Weight</a>
            <a href="#" class="list-group-item list-group-item-action bg-light">info1</a>
        <a href="#" class="list-group-item list-group-item-action bg-light">info2</a>-->
      </div>
      <div style="margin-top: 25%;" class="sidebar-heading">
        <b>Filter Collection</b>
      </div>
      <div>
        <button
          style="color:blue;"
          class="list-group-item list-group-item-action bg-light"
          type="button"
        >
          <b>disease1</b>
        </button>
        <button
          style="color:red;"
          class="list-group-item list-group-item-action bg-light"
          type="button"
        >
          <b>disease2</b>
        </button>
        <button
          style="color:green;"
          class="list-group-item list-group-item-action bg-light"
          type="button"
        >
          <b>disease3</b>
        </button>
        <button
          style="color:brown;"
          class="list-group-item list-group-item-action bg-light"
          type="button"
        >
          <b>disease4</b>
        </button>
      </div>
    </div>

    <div style="margin-left:2%">
      <line-chart></line-chart>
    </div>

    <div style="margin-left:5%; margin-left:2%; width: 35%;" id="1">
      <h5 style="background-color:blue">We will be inserting the text details here
      <br>
      We will be inserting the text details here
      <br>
      We will be inserting the text details here
      <br>
      We will be inserting the text details here
      </h5>
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
