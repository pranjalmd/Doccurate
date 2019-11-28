<template>
  <div id="my_dataviz"></div>
</template>


<script>
import * as d3 from "d3";

export default {
  name: "LineChart",
  props: {
    data: {
      required: false,
      default: null
    }
  },
  mounted: function() {
        // set the dimensions and margins of the graph
        var margin = {top: 10, right: 30, bottom: 30, left: 40},
            width = 460 - margin.left - margin.right,
            height = 400 - margin.top - margin.bottom;

        // append the svg object to the body of the page
        var svg = d3.select("#my_dataviz")
          .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
          .append("g")
            .attr("transform",
                  "translate(" + margin.left + "," + margin.top + ")");

        // Read the data and compute summary statistics for each specie
        d3.csv("https://raw.githubusercontent.com/holtzy/D3-graph-gallery/master/DATA/iris.csv", function(data) {
          console.log(data);
          // Build and Show the Y scale
          var y = d3.scaleLinear()
            .domain([ 3.5,8 ])          // Note that here the Y scale is set manually
            .range([height, 20])
          svg.append("g").call( d3.axisLeft(y) )

          // Build and Show the X scale. It is a band scale like for a boxplot: each group has an dedicated RANGE on the axis. This range has a length of x.bandwidth
          var x = d3.scaleBand()
            .range([ 0, width ])
            .domain(["setosa", "versicolor", "virginica"])
            .padding(0.05)     // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.

          var x_top = d3.scaleBand()
            .range([ 0, width ])
            .domain(["setosa", "versicolor", "virginica"])
            .padding(0.05)     // This is important: it is the space between 2 groups. 0 means no padding. 1 is the maximum.


          // svg.append("g")
          //   .attr("transform", "translate(0," + height + ")")
          //   .call(d3.axisBottom(x))

          svg.append("g")
            .attr("transform", "translate(0," + 10 + ")")
            .attr("class", "axisRed")
            .call(d3.axisTop(x_top))
          // Features of the histogram
          var histogram = d3.histogram()
                .domain(y.domain())
                .thresholds(y.ticks(20))    // Important: how many bins approx are going to be made? It is the 'resolution' of the violin plot
                .value(d => d)

          // Compute the binning for each group of the dataset
          var sumstat = d3.nest()  // nest function allows to group the calculation per level of a factor
            .key(function(d) { return d.Species;})
            .rollup(function(d) {   // For each key..
            
              var input = d.map(function(g) { return g.Sepal_Length;})    // Keep the variable called Sepal_Length
              var bins = histogram(input)   // And compute the binning on it.
              return(bins)
            })
            .entries(data)

          // What is the biggest number of value in a bin? We need it cause this value will have a width of 100% of the bandwidth.
          var maxNum = 0
          for ( let i in sumstat ){
            var allBins = sumstat[i].value
            console.log("All Bins: ",allBins);
            var lengths = allBins.map(function(a){return a.length;})
            var longuest = d3.max(lengths)
            if (longuest > maxNum) { maxNum = longuest }
          }

          // The maximum width of a violin must be x.bandwidth = the width dedicated to a group
          var xNum = d3.scaleLinear()
            .range([0, x.bandwidth()])
            .domain([-maxNum,maxNum])

          // This allows to find the closest X index of the mouse:
          var bisect = d3.bisector(function(d) { return d.x; }).left;

          // var tip = d3.tip()
          //             .attr('class', 'd3-tip')
          //             .offset([-10, 0])
          //             .html(function(d) {
          //               return "<strong>Frequency:</strong> <span style='color:red'>" + d.frequency + "</span>";
          //             })
          // svg.call(tip);

          var div_selected = d3.select("#my_dataviz").append("div")
    .attr("class", "tooltip")
    .style("opacity", 0);
          // Add the shape to this svg!
          svg
            .selectAll("myViolin")
            .data(sumstat)
            .enter()        // So now we are working group per group
            .append("g")
              .attr("transform", function(d){ return("translate(" + x(d.key) +" ,0)") } ) // Translation on the right to be at the group position
            .append("path")
                .datum(function(d){ return(d.value)})     // So now we are working bin per bin
                .style("stroke", "none")
                .style("fill","#69b3a2")
                .attr("d", d3.area()
                    .x0(function(d){ 
                      if (d.length == 0){
                        return (xNum(0.1))
                      }
                      return(xNum(-d.length)) 
                      } )
                    .x1(function(d){ return(xNum(d.length)) } )
                    .y(function(d){ return(y(d.x0)) } )
                    .curve(d3.curveCatmullRom)    // This makes the line smoother to give the violin appearance. Try d3.curveStep to see the difference
                )
      
                .on('mousemove', function (data,i) {
                  console.log("Data", data);
                  var mappedVales = data.map(x => x.x0);
                  
                  var y_scaled = y.invert(d3.mouse(this)[1]);
                  console.log("Y" ,y_scaled, mappedVales);
                  var index = d3.bisectLeft(mappedVales, y_scaled);
                  var selectedData = mappedVales[index]
                  console.log("Selected Index : ", index, selectedData);
  
                  div_selected.transition()
                              .duration(200)
                              .style("opacity", .9);
                    div_selected.html("Hello" + "<br/>" + data[index].length)
                      .style("left", (d3.event.pageX) + "px")
                      .style("top", (d3.event.pageY) + "px");
                })
                .on("mouseout", function(n) { div_selected.transition()
                                                .duration(500)
                                                .style("opacity", 0);
                });

        })

    }
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