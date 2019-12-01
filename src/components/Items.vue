<template>
  <div>
    <!-- <p>{{this.props.data}}</p> -->
    <p>{{this.mydata["start"]}}</p>
    <h1>{{this.mydata["stop"]}}</h1>
    <p>{{this.mydata["Date_of_birth"]}}</p>
    <p>{{this.mydata["Medical record"]}}</p>
    <!-- <p>{{this.mydata[]}}</p> -->
    <hr/>
    <hr/>
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

</style>
