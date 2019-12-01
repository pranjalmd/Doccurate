<template>
  <div class="box">
    <!-- <p>{{this.props.data}}</p> -->
    <h4>{{this.mydata["ENCOUNTER"]}}</h4>
    <p>{{this.mydata["start"]}}</p>
    <p>{{this.mydata["stop"]}}</p>
    <p>{{this.mydata["Date_of_birth"]}}</p>
    <p>{{this.mydata["Medical record"]}}</p>
  </div>
</template>


<script>

export default {
  name: "item",
  props: {
    mydata: Object
  },
  methods: {
    tempfn: function(){
      console.log(this.mydata);
    }
  },
  mounted: function(){
      this.tempfn()
  }
};

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.box:nth-of-type(odd) {
background-color: rgba(200,200,200,0.8);
}
    
.box:nth-of-type(even) {
background-color:rgba(200,200,200,0.1);
}

.box {
padding: 10px;
margin-top: 20px;
border: 2px solid rgba(200,200,200,0.9);
}
</style>
