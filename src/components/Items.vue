<template>
  <div class="box">
    <text-highlight :queries="keywords" >{{ this.mydata["Medical record"] }}</text-highlight>
    <!-- <p>{{this.props.data}}</p> -->
    <h4>{{this.keywords["ENCOUNTER"]}}</h4>
    <p>{{this.mydata["start"]}}</p>
    <p>{{this.mydata["stop"]}}</p>
    <p>{{this.mydata["Date_of_birth"]}}</p>
   
  </div>
</template>


<script>
import Vue from 'vue';
import TextHighlight from 'vue-text-highlight';
 
Vue.component('text-highlight', TextHighlight);

export default {
  name: "item",
  props: {
    mydata: Object,
    keywords: {
        type: Array,
        default: [1]
    }
  },
  data() {
        return {
            queries: ['birds', 'scatt'],
            description: 'Tropical birds scattered as Drake veered the Jeep',
            activeColor: 'red',
            fontSize: 30
        };
    },
  methods: {
    tempfn: function(){
      console.log(this.keywords);
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
