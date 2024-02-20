<script>
  import { onMount } from 'svelte';
  import { bind } from 'svelte';
  import * as d3 from 'd3';

  // Initialize data
  let data = [];
  let x, y, curve;
  const margin = { top: 30, right: 30, bottom: 30, left: 50 };
  const width = 460 - margin.left - margin.right;
  const height = 400 - margin.top - margin.bottom;
  let binNumber = 50;

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
      .attr("d",  d3.line()
        .x(function(d) { return x(d[0]); })
        .y(function(d) { return y(d[1]); })
      );
  }

  // Load data on mount
  onMount(() => {
    d3.csv("df.csv").then(rawData => {
      data = rawData.map(d => +d.GDP); // Assuming "GDP" is the column containing GDP data
      x = d3.scaleLinear()
        .domain([d3.min(data), d3.max(data)])
        .range([0, width]);

      y = d3.scaleLinear()
        .range([height, 0])
        .domain([0, 0.01]);

      const svg = d3.select("#my_dataviz")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom);

      svg.append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")")
        .append("path")
        .attr("class", "mypath")
        .attr("fill", "#69b3a2")
        .attr("opacity", ".8")
        .attr("stroke", "#000")
        .attr("stroke-width", 1);

      curve = svg.select(".mypath");

      createVisualization(binNumber); // Initial visualization with default bin number
    });
  });
</script>

<!-- Add a slider -->
<input type="range" name="mySlider" id="mySlider" min="10" max="100" value="50" bind:value={binNumber} />

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>

