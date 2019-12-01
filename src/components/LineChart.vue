<template>
<div>
  <button type="button" @click="onClickBack">BACK!</button>
  <div id="my_dataviz"></div>
    
 </div>
</template>

<script>
import * as d3 from "d3";
import { parse } from 'path';
import { constants } from 'fs';
var lvl = "FC Tuples";
var level_selected = 0;
var level_value = "";
var parent_stack = []
var x_domain_values = [];
var initial_max = 0;

export default {
  name: "LineChart",
  props: {
    data: {
      required: false,
      default: null
    }
  },
  methods: {
    onClickBack: function(){
      if(level_selected <= 0){
        return;
      }
      level_selected -= 1;
      console.log("Parent Stack ", parent_stack);
      level_value = parent_stack.pop();
      console.log(level_selected, level_value)
      d3.select("#my_dataviz").selectAll("svg").remove();
      this.updateDataFunc();
    },

    updateDataFunc :  function updateData(){
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 30, bottom: 30, left: 40 },
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      // append the svg object to the body of the page
      var svg = d3
        .select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");


        var svg_transition = d3.select("#my_dataviz").transition();
        d3.csv("final_1.csv",
            function(data_patient) {
              // console.log("patient 1 csv", data_patient);

              var FCs = data_patient.map(function(d){
                return d.Filter_Collection;
              })
              var FCs_unique = [...new Set(FCs)]
              // console.log("FCs", FCs_unique);

          // Read the data and compute summary statistics for each specie
          d3.csv(
            "https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/iris.csv",
            function(data) {



              var y = d3.scaleTime()
                      .domain(d3.extent(data_patient, function(d) { 
                              // return new Date(d.start);
                              let mydate = Date.parse(d.start, "mm/dd/yyyy");
                            
                            
                              return mydate
                              }))
                              .range([50, width]);
              svg.append("g").call(d3.axisLeft(y));
          



              // Features of the histogram
              var histogram = d3
                .histogram()
                .domain(function(){
                  // console.log("Hist Domain",y.domain().map(function(d){ return Date.parse(d)}));
                  
                  return y.domain().map(function(d){ return Date.parse(d);});
                })
                .thresholds(y.ticks(20)) // Important: how many bins approx are going to be made? It is the 'resolution' of the violin plot
                .value(function(d){

                  // console.log("Hist",d);
                  return d;
                });



            var sumstat = d3
                .nest() // nest function allows to group the calculation per level of a factor
                .key(function(d,i) {
                  // return d.Species;
                  if(level_selected == 1 && d["FC Tuples"] == level_value){
                      return d["Disease Tuples"].trim();
                  }else if(level_selected == 1){
                    return " ";
                  }
                  // console.log(d["Disease Tuples"],level_value);
                  if(level_selected == 2 && d["Disease Tuples"].trim() == level_value){
                     console.log("Disease-------",d);
                      return d["Sympton Tuples"].trim();
                  }else if(level_selected == 2){
                    return " ";
                  }

                  
                  if(d[lvl]){
                    // console.log(i, d[lvl]);
                    return d[lvl];
                  }else{
                    return " ";
                  }
                })
                .rollup(function(d) {
                  // For each key..

                  var input = d.map(function(g) {
                    // return g.Sepal_Length;
                    return Date.parse(g.start, "mm/dd/yyyy");
                  }); // Keep the variable called Sepal_Length
                  // console.log("Input", input);
                  var bins = histogram(input); // And compute the binning on it.
                  // console.log(bins);
                  return bins;
                })
                // .entries(data);
                .entries(data_patient);
            console.log("afsdfasdf",sumstat);

            x_domain_values = []
            var del_index = -1
            for( let index in sumstat){
                if(sumstat[index]['key'] == " "){
                  console.log("Found",sumstat[index], index);
                  del_index = index;
                  console.log("XXXXXX to x_domain",sumstat[index]['key']);
                  
                }
                else{
                    x_domain_values.push(sumstat[index]['key'])
                    console.log("Added to x_domain",sumstat[index]['key']);
                }
                // console.log("afsdfasdf",sumstat[index], index);
              }
              sumstat.splice(del_index, 1);
        
              // //Creates Bins
              // var fc_dic = {}
              // // console.log(data_patient);
              // for (var i =0 ; i < data_patient.length; i++){
              //   // console.log("Patient", data_patient[i]);
              //   var fc_tuple = data_patient[i]['FC Tuples'];
              //   var fc_list = fc_tuple.split(" ");
              //   for(var j = 0; j < fc_list.length; j++){
              //     if(fc_list[j] in fc_dic){
              //       fc_dic[fc_list[j]].push(data_patient[i])  
              //     }
              //     else{
              //       fc_dic[fc_list[j]] = [data_patient[i]]
              //     }
              //   }
                

              // }
              // console.log(fc_dic);

              // var sumstat_var = []
              // for(var i in fc_dic){
              //   var temp_dic = {}
              //   if(i == ""){
              //     continue;
              //   }
              //   temp_dic['key'] = i;

              //   var input = fc_dic[i].map(function(g) {
              //       // return g.Sepal_Length;
              //       return Date.parse(g.start, "mm/dd/yyyy");
              //     }); // Keep the variable called Sepal_Length
              //     // console.log("Input", input);
              //   var bins = histogram(input); // And compute the binning on it.
              //   temp_dic['value'] = bins;
              //   sumstat_var.push(temp_dic);
              // }
              // console.log("$$$$$",sumstat_var)





              // What is the biggest number of value in a bin? We need it cause this value will have a width of 100% of the bandwidth.
              var maxNum = 0;
              console.log("Sum Stats",sumstat);
              
              for (let i in sumstat) {
                var allBins = sumstat[i].value;
                // console.log("All Bins: ", allBins);
                var lengths = allBins.map(function(a) {
                  return a.length;
                });
                var longuest = d3.max(lengths);
                if (longuest > maxNum) {
                  maxNum = longuest;
                }
              }

              if (level_selected == 0){
                initial_max = maxNum
              }







          

              // Build and Show the X scale. It is a band scale like for a boxplot: each group has an dedicated RANGE on the axis. This range has a length of x.bandwidth
              var x_domain_values_new = x_domain_values.map(function(d){
                // console.log("Map Func",typeof(d));
                return d.trim();
              })
              var zp = 0;
              x_domain_values_new.map(function(d){
                console.log("Map Func:",d,":", zp++);
                // return d.trim();
              })
              var x = d3
                .scaleBand()
                .range([0, width])
                // .domain(["setosa", "versicolor", "virginica"])
                .domain(x_domain_values_new)
                .padding(0.05); // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.

              var x_top = d3
                .scaleBand()
                .range([0, width])
                // .domain(["setosa", "versicolor", "virginica"])
                .domain(x_domain_values_new)
                .padding(0.05); // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.

              console.log("%%%%%%%%%% ::", x_top("Angiomyolipoma") );
              // svg.append("g")
              //   .attr("transform", "translate(0," + height + ")")
              //   .call(d3.axisBottom(x))
              var x_label_axis = svg
                                  .append("g")
                                  .attr("transform", "translate(0," + 30 + ")")
                                  .attr("class", "axisRed")
                                  .call(d3.axisTop(x_top))
                                  .selectAll("text")
                                  .attr("transform", "rotate(-30)")
                                  .on("click", function(d,i) {  
                                    if(level_selected ==2){
                                      return;
                                    }
                                    parent_stack.push(level_value);
                                    // console.log("Parent Stack ", parent_stack);
                                    level_selected += 1;
                                    level_value = d;
                                    // parent_stack.push(d);
                                    console.log("Parent Stack ", parent_stack);
                                    d3.select("#my_dataviz").selectAll("svg").remove();
                                    console.log("Updated Variables : ", level_selected, level_value)
                                    updateData();

                                    // renderVisualization(data_patient);
                                  });

      
              


            


              // Compute the binning for each group of the dataset
            















              // The maximum width of a violin must be x.bandwidth = the width dedicated to a group
              var xNum = d3
                .scaleLinear()
                .range([0, x.bandwidth()])
                .domain([-initial_max, initial_max]);

              // This allows to find the closest X index of the mouse:
              var bisect = d3.bisector(function(d) {
                return d.x;
              }).left;

              // var tip = d3.tip()
              //             .attr('class', 'd3-tip')
              //             .offset([-10, 0])
              //             .html(function(d) {
              //               return "<strong>Frequency:</strong> <span style='color:red'>" + d.frequency + "</span>";
              //             })
              // svg.call(tip);

              var div_selected = d3
                .select("#my_dataviz")
                .append("div")
                .attr("class", "tooltip")
                .style("opacity", 0);
              // Add the shape to this svg!
              svg
                .selectAll("myViolin")
                .data(sumstat)
                .enter() // So now we are working group per group
                .append("g")
                .attr("transform", function(d) {
                  console.log(d,d.key,  x_top(d.key));
                  return "translate(" + x_top(d.key) + " ,0)";
                }) // Translation on the right to be at the group position
                .append("path")
                .datum(function(d) {
                  return d.value;
                }) // So now we are working bin per bin
                .style("stroke", "none")
                .style("fill", "#69b3a2")
                .attr(
                  "d",
                  d3
                    .area()
                    .x0(function(d) {
                      if (d.length == 0) {
                        return xNum(0.1);
                      }
                      return xNum(-d.length);
                    })
                    .x1(function(d) {
                      return xNum(d.length);
                    })
                    .y(function(d) {
                      return y(d.x0);
                    })
                    .curve(d3.curveCatmullRom) // This makes the line smoother to give the violin appearance. Try d3.curveStep to see the difference
                )

                .on("mousemove", function(data, i) {
                  // console.log("Data", data);
                  var mappedVales = data.map(x => x.x0);

                  var y_scaled = y.invert(d3.mouse(this)[1]);
                  // console.log("Y", y_scaled, mappedVales);
                  var index = d3.bisectLeft(mappedVales, y_scaled);
                  var selectedData = mappedVales[index];
                  // console.log("Selected Index : ", index, selectedData);

                  div_selected
                    .transition()
                    .duration(200)
                    .style("opacity", 0.9);
                  div_selected
                    .html("Hello" + "<br/>" + data[index].length)
                    .style("left", d3.event.pageX + "px")
                    .style("top", d3.event.pageY + "px");
                })
                .on("mouseout", function(n) {
                  div_selected
                    .transition()
                    .duration(500)
                    .style("opacity", 0);
                });
            }
          );
      });
      //horizontal bar chart
      var margin_for_bar_chart = { top: 5, right: 5, bottom: 5, left: 5 },
        width_for_bar_chart = 200 - margin.left - margin.right,
        height_for_bar_chart = 450 - margin.top - margin.bottom;

      var svg1 = d3
        .select("#my_dataviz")
        .append("svg")
        .attr(
          "width",
          width_for_bar_chart +
            margin_for_bar_chart.left +
            margin_for_bar_chart.right
        )
        .attr(
          "height",
          height_for_bar_chart +
            margin_for_bar_chart.top +
            margin_for_bar_chart.bottom
        )
        .append("g")
        .attr(
          "transform",
          "translate(" +
            margin_for_bar_chart.left +
            "," +
            margin_for_bar_chart.top +
            ")"
        );

      var x = d3.scaleLinear().range([0, width_for_bar_chart]);
      var y = d3.scaleBand().range([height_for_bar_chart, 0]);

      d3.csv("temp_bar_data.csv", function(error, data) {
        if (error) throw error;

        data.forEach(function(d) {
          d.name = d.name;
          d.val = +d.val;
        });

        data.sort(function(a, b) {
          return a.val - b.val;
        });

        x.domain([
          0,
          d3.max(data, function(d) {
            return d.val;
          })
        ]);
        y.domain(
          data.map(function(d, i) {
            return i;
          })
        ).padding(0.1);

        svg1
          .selectAll(".bar")
          .data(data)
          .enter()
          .append("rect")
          .attr("class", "bar")
          .attr("x", 0)
          .style("fill", "pink")
          .attr("height", y.bandwidth() - 15)
          .attr("y", function(d, i) {
            return y(i);
          })
          .attr("width", function(d) {
            return x(d.val);
          });

        svg1
          .selectAll("text")
          .data(data)
          .enter()
          .append("text")
          .text(function(d) {
            return d.name;
          })
          .attr("x", 0)
          .attr("y", function(d, i) {
            return y(i) + 12;
          })
          .style("fill", "green");
        //horizontal bar chart end
      });//horizontal bar chart csv closed

      //patient info tab dynamic property
      d3.select("#patientinfo")
        .selectAll("*")
        .remove();

      d3.csv("Patient1_info.csv", function(error, data) {
        if (error) throw error;

        data.forEach(function(d) {
          d.key = d.key.toUpperCase();
          d.val = d.val;
        });

        var pat = d3
          .select("#patientinfo")
          .selectAll("h6")
          .data(data)
          .enter()
          .append("h6")
          .style("width", "150px")
          .style("margin-top", "11%")
          .style("margin-left", "2%")
          .text(function(d) {
            return d.key + ": ";
          })
          .style("text-align", "left")
          .style("font-weight", "bold")
          .append()
          .text(function(d) {
            return d.val;
          })
          .style("font-weight", "normal")
          .style("text-align", "left")
          
        //patient info tab dynamic property end
      });//patient_info csv closed

      d3.csv("Patient1_text.csv", function(error, data) {
        if (error) throw error;

        d3.select("#textdetails")
          .selectAll("h6")
          .data(data)
          .enter()
          .append("h6")
          .text(function(d) {
            return d.key + ": ";
          })
          .style("text-align", "left")
          .style("margin-top", "3%")
          .style("font-weight", "bold")
          .append()
          .text(function(d){
            return d.val;
          })
          .style("font-weight", "normal");
          
      });//patient text data csv closed
      
    }
  },
  mounted: function() {
    // var lvl = "FC Tuples";
    // var level_selected = 0;
    // var level_value = "Nephrology";
    // var x_domain_values = []
/*
    //  Selected data and selected level;
    function updateData(){
      // set the dimensions and margins of the graph
      var margin = { top: 10, right: 30, bottom: 30, left: 40 },
        width = 460 - margin.left - margin.right,
        height = 400 - margin.top - margin.bottom;

      // append the svg object to the body of the page
      var svg = d3
        .select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");


        var svg_transition = d3.select("#my_dataviz").transition();
        d3.csv("final_1.csv",
            function(data_patient) {
              // console.log("patient 1 csv", data_patient);

              var FCs = data_patient.map(function(d){
                return d.Filter_Collection;
              })
              var FCs_unique = [...new Set(FCs)]
              // console.log("FCs", FCs_unique);

          // Read the data and compute summary statistics for each specie
          d3.csv(
            "https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/iris.csv",
            function(data) {



              var y = d3.scaleTime()
                      .domain(d3.extent(data_patient, function(d) { 
                              // return new Date(d.start);
                              let mydate = Date.parse(d.start, "mm/dd/yyyy");
                            
                            
                              return mydate
                              }))
                              .range([50, width]);
              svg.append("g").call(d3.axisLeft(y));
          



              // Features of the histogram
              var histogram = d3
                .histogram()
                .domain(function(){
                  // console.log("Hist Domain",y.domain().map(function(d){ return Date.parse(d)}));
                  
                  return y.domain().map(function(d){ return Date.parse(d);});
                })
                .thresholds(y.ticks(20)) // Important: how many bins approx are going to be made? It is the 'resolution' of the violin plot
                .value(function(d){

                  // console.log("Hist",d);
                  return d;
                });



            var sumstat = d3
                .nest() // nest function allows to group the calculation per level of a factor
                .key(function(d,i) {
                  // return d.Species;
                  if(level_selected == 1 && d["FC Tuples"] == level_value){
                      return d["Disease Tuples"].trim();
                  }else if(level_selected == 1){
                    return " ";
                  }
                  // console.log(d["Disease Tuples"],level_value);
                  if(level_selected == 2 && d["Disease Tuples"].trim() == level_value){
                     console.log("Disease-------",d);
                      return d["Sympton Tuples"].trim();
                  }else if(level_selected == 2){
                    return " ";
                  }

                  
                  if(d[lvl]){
                    // console.log(i, d[lvl]);
                    return d[lvl];
                  }else{
                    return " ";
                  }
                })
                .rollup(function(d) {
                  // For each key..

                  var input = d.map(function(g) {
                    // return g.Sepal_Length;
                    return Date.parse(g.start, "mm/dd/yyyy");
                  }); // Keep the variable called Sepal_Length
                  // console.log("Input", input);
                  var bins = histogram(input); // And compute the binning on it.
                  // console.log(bins);
                  return bins;
                })
                // .entries(data);
                .entries(data_patient);
            console.log("afsdfasdf",sumstat);

            x_domain_values = []
            var del_index = -1
            for( let index in sumstat){
                if(sumstat[index]['key'] == " "){
                  console.log("Found",sumstat[index], index);
                  del_index = index;
                  console.log("XXXXXX to x_domain",sumstat[index]['key']);
                  
                }
                else{
                    x_domain_values.push(sumstat[index]['key'])
                    console.log("Added to x_domain",sumstat[index]['key']);
                }
                // console.log("afsdfasdf",sumstat[index], index);
              }
              sumstat.splice(del_index, 1);
        
              // //Creates Bins
              // var fc_dic = {}
              // // console.log(data_patient);
              // for (var i =0 ; i < data_patient.length; i++){
              //   // console.log("Patient", data_patient[i]);
              //   var fc_tuple = data_patient[i]['FC Tuples'];
              //   var fc_list = fc_tuple.split(" ");
              //   for(var j = 0; j < fc_list.length; j++){
              //     if(fc_list[j] in fc_dic){
              //       fc_dic[fc_list[j]].push(data_patient[i])  
              //     }
              //     else{
              //       fc_dic[fc_list[j]] = [data_patient[i]]
              //     }
              //   }
                

              // }
              // console.log(fc_dic);

              // var sumstat_var = []
              // for(var i in fc_dic){
              //   var temp_dic = {}
              //   if(i == ""){
              //     continue;
              //   }
              //   temp_dic['key'] = i;

              //   var input = fc_dic[i].map(function(g) {
              //       // return g.Sepal_Length;
              //       return Date.parse(g.start, "mm/dd/yyyy");
              //     }); // Keep the variable called Sepal_Length
              //     // console.log("Input", input);
              //   var bins = histogram(input); // And compute the binning on it.
              //   temp_dic['value'] = bins;
              //   sumstat_var.push(temp_dic);
              // }
              // console.log("$$$$$",sumstat_var)





              // What is the biggest number of value in a bin? We need it cause this value will have a width of 100% of the bandwidth.
              var maxNum = 0;
              console.log("Sum Stats",sumstat);
              
              for (let i in sumstat) {
                var allBins = sumstat[i].value;
                // console.log("All Bins: ", allBins);
                var lengths = allBins.map(function(a) {
                  return a.length;
                });
                var longuest = d3.max(lengths);
                if (longuest > maxNum) {
                  maxNum = longuest;
                }
              }

              if (level_selected == 0){
                initial_max = maxNum
              }







          

              // Build and Show the X scale. It is a band scale like for a boxplot: each group has an dedicated RANGE on the axis. This range has a length of x.bandwidth
              var x_domain_values_new = x_domain_values.map(function(d){
                // console.log("Map Func",typeof(d));
                return d.trim();
              })
              var zp = 0;
              x_domain_values_new.map(function(d){
                console.log("Map Func:",d,":", zp++);
                // return d.trim();
              })
              var x = d3
                .scaleBand()
                .range([0, width])
                // .domain(["setosa", "versicolor", "virginica"])
                .domain(x_domain_values_new)
                .padding(0.05); // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.

              var x_top = d3
                .scaleBand()
                .range([0, width])
                // .domain(["setosa", "versicolor", "virginica"])
                .domain(x_domain_values_new)
                .padding(0.05); // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.

              console.log("%%%%%%%%%% ::", x_top("Angiomyolipoma") );
              // svg.append("g")
              //   .attr("transform", "translate(0," + height + ")")
              //   .call(d3.axisBottom(x))
              var x_label_axis = svg
                                  .append("g")
                                  .attr("transform", "translate(0," + 30 + ")")
                                  .attr("class", "axisRed")
                                  .call(d3.axisTop(x_top))
                                  .selectAll("text")
                                  .attr("transform", "rotate(-30)")
                                  .on("click", function(d,i) {  
                                    // alert(d + i);
                                    
                                    level_selected += 1;
                                    level_value = d;
                                    parent_stack.push(d);
                                    d3.select("#my_dataviz").selectAll("svg").remove();
                                    console.log("Updated Variables : ", level_selected, level_value)
                                    updateData();

                                    // renderVisualization(data_patient);
                                  });

      
              


            


              // Compute the binning for each group of the dataset
            















              // The maximum width of a violin must be x.bandwidth = the width dedicated to a group
              var xNum = d3
                .scaleLinear()
                .range([0, x.bandwidth()])
                .domain([-initial_max, initial_max]);

              // This allows to find the closest X index of the mouse:
              var bisect = d3.bisector(function(d) {
                return d.x;
              }).left;

              // var tip = d3.tip()
              //             .attr('class', 'd3-tip')
              //             .offset([-10, 0])
              //             .html(function(d) {
              //               return "<strong>Frequency:</strong> <span style='color:red'>" + d.frequency + "</span>";
              //             })
              // svg.call(tip);

              var div_selected = d3
                .select("#my_dataviz")
                .append("div")
                .attr("class", "tooltip")
                .style("opacity", 0);
              // Add the shape to this svg!
              svg
                .selectAll("myViolin")
                .data(sumstat)
                .enter() // So now we are working group per group
                .append("g")
                .attr("transform", function(d) {
                  console.log(d,d.key,  x_top(d.key));
                  return "translate(" + x_top(d.key) + " ,0)";
                }) // Translation on the right to be at the group position
                .append("path")
                .datum(function(d) {
                  return d.value;
                }) // So now we are working bin per bin
                .style("stroke", "none")
                .style("fill", "#69b3a2")
                .attr(
                  "d",
                  d3
                    .area()
                    .x0(function(d) {
                      if (d.length == 0) {
                        return xNum(0.1);
                      }
                      return xNum(-d.length);
                    })
                    .x1(function(d) {
                      return xNum(d.length);
                    })
                    .y(function(d) {
                      return y(d.x0);
                    })
                    .curve(d3.curveCatmullRom) // This makes the line smoother to give the violin appearance. Try d3.curveStep to see the difference
                )

                .on("mousemove", function(data, i) {
                  // console.log("Data", data);
                  var mappedVales = data.map(x => x.x0);

                  var y_scaled = y.invert(d3.mouse(this)[1]);
                  // console.log("Y", y_scaled, mappedVales);
                  var index = d3.bisectLeft(mappedVales, y_scaled);
                  var selectedData = mappedVales[index];
                  // console.log("Selected Index : ", index, selectedData);

                  div_selected
                    .transition()
                    .duration(200)
                    .style("opacity", 0.9);
                  div_selected
                    .html("Hello" + "<br/>" + data[index].length)
                    .style("left", d3.event.pageX + "px")
                    .style("top", d3.event.pageY + "px");
                })
                .on("mouseout", function(n) {
                  div_selected
                    .transition()
                    .duration(500)
                    .style("opacity", 0);
                });
            }
          );
      });
      //horizontal bar chart
      var margin_for_bar_chart = { top: 5, right: 5, bottom: 5, left: 5 },
        width_for_bar_chart = 200 - margin.left - margin.right,
        height_for_bar_chart = 450 - margin.top - margin.bottom;

      var svg1 = d3
        .select("#my_dataviz")
        .append("svg")
        .attr(
          "width",
          width_for_bar_chart +
            margin_for_bar_chart.left +
            margin_for_bar_chart.right
        )
        .attr(
          "height",
          height_for_bar_chart +
            margin_for_bar_chart.top +
            margin_for_bar_chart.bottom
        )
        .append("g")
        .attr(
          "transform",
          "translate(" +
            margin_for_bar_chart.left +
            "," +
            margin_for_bar_chart.top +
            ")"
        );

      var x = d3.scaleLinear().range([0, width_for_bar_chart]);
      var y = d3.scaleBand().range([height_for_bar_chart, 0]);

      d3.csv("temp_bar_data.csv", function(error, data) {
        if (error) throw error;

        data.forEach(function(d) {
          d.name = d.name;
          d.val = +d.val;
        });

        data.sort(function(a, b) {
          return a.val - b.val;
        });

        x.domain([
          0,
          d3.max(data, function(d) {
            return d.val;
          })
        ]);
        y.domain(
          data.map(function(d, i) {
            return i;
          })
        ).padding(0.1);

        svg1
          .selectAll(".bar")
          .data(data)
          .enter()
          .append("rect")
          .attr("class", "bar")
          .attr("x", 0)
          .style("fill", "pink")
          .attr("height", y.bandwidth() - 15)
          .attr("y", function(d, i) {
            return y(i);
          })
          .attr("width", function(d) {
            return x(d.val);
          });

        svg1
          .selectAll("text")
          .data(data)
          .enter()
          .append("text")
          .text(function(d) {
            return d.name;
          })
          .attr("x", 0)
          .attr("y", function(d, i) {
            return y(i) + 12;
          })
          .style("fill", "green");
        //horizontal bar chart end
      });//horizontal bar chart csv closed

      //patient info tab dynamic property
      d3.select("#patientinfo")
        .selectAll("*")
        .remove();

      d3.csv("Patient1_info.csv", function(error, data) {
        if (error) throw error;

        data.forEach(function(d) {
          d.key = d.key.toUpperCase();
          d.val = d.val;
        });

        var pat = d3
          .select("#patientinfo")
          .selectAll("h6")
          .data(data)
          .enter()
          .append("h6")
          .style("width", "150px")
          .style("margin-top", "11%")
          .style("margin-left", "2%")
          .text(function(d) {
            return d.key + ": ";
          })
          .style("text-align", "left")
          .style("font-weight", "bold")
          .append()
          .text(function(d) {
            return d.val;
          })
          .style("font-weight", "normal")
          .style("text-align", "left")
          
        //patient info tab dynamic property end
      });//patient_info csv closed

      d3.csv("Patient1_text.csv", function(error, data) {
        if (error) throw error;

        d3.select("#textdetails")
          .selectAll("h6")
          .data(data)
          .enter()
          .append("h6")
          .text(function(d) {
            return d.key + ": ";
          })
          .style("text-align", "left")
          .style("margin-top", "3%")
          .style("font-weight", "bold")
          .append()
          .text(function(d){
            return d.val;
          })
          .style("font-weight", "normal");
          
      });//patient text data csv closed
      
    }

  
  */
  this.updateDataFunc();

  
 
  } //end of the mounted function
  
};
</script>

<style>
body {
  font: 10px sans-serif;
}

.axisRed .domain {
  fill: none;
  stroke: #3f3;
  opacity: 0;
  shape-rendering: crispEdges;
}

.axisRed .tick line {
  fill: none;
  stroke: #3f3;
  opacity: 0;
  shape-rendering: crispEdges;
}

div.tooltip {
  position: absolute;
  text-align: center;
  width: 60px;
  height: 28px;
  padding: 2px;
  font: 12px sans-serif;
  background: lightsteelblue;
  border: 0px;
  border-radius: 8px;
  pointer-events: none;
}
</style>