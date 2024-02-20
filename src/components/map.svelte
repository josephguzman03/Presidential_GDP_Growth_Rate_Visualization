<script>
      import { onMount } from 'svelte';
      import * as d3 from 'd3';
    
      // Initialize data
      let data = [];
      let x, y, curve;
    
      // Function to compute density
      function kernelDensityEstimator(kernel, X) {
        return function(V) {
          return X.map(function(x) {
            return [x, d3.mean(V, function(v) { return kernel(x - v); })];
          });
        };
      }
    
      function kernelEpanechnikov(k) {
        return function(v) {
          return Math.abs(v /= k) <= 1 ? 0.75 * (1 - v * v) / k : 0;
        };
      }
    
      // Function to create visualization
      function createVisualization(binNumber) {
        const kde = kernelDensityEstimator(kernelEpanechnikov(7), x.ticks(binNumber))
        const density = kde(data);
        curve
          .datum(density)
          .transition()
          .duration(1000)
          .attr("d",  d3.line()
            .x(function(d) { return x(d[0]); })
            .y(function(d) { return y(d[1]); })
          );
      }
    
      // Load data on mount
      onMount(() => {
        d3.csv("df.csv", d => {
          data.push(+d.GDP);
        }).then(() => {
          x = d3.scaleLinear()
            .domain([d3.min(data), d3.max(data)])
            .range([0, width]);
    
          y = d3.scaleLinear()
            .range([height, 0])
            .domain([0, 0.01]);
    
          curve = d3.select("#my_dataviz")
            .append("svg")
            .attr("width", width + margin.left + margin.right)
            .attr("height", height + margin.top + margin.bottom)
            .append("g")
            .attr("transform",
                  "translate(" + margin.left + "," + margin.top + ")")
            .append("path")
            .attr("class", "mypath")
            .attr("fill", "#69b3a2")
            .attr("opacity", ".8")
            .attr("stroke", "#000")
            .attr("stroke-width", 1);
    
          createVisualization(50); // Initial visualization with default bin number
    
        });
      });
    </script>
    
        
    <!-- Add a slider -->
    <input type="range" name="mySlider" id="mySlider" min="10" max="100" value="50">
    
    <!-- Create a div where the graph will take place -->
    <div id="my_dataviz"></div>