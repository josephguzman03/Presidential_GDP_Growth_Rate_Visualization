<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    let data = [];
    let presidents = [];
    let x, y, xAxis, yAxis, line;
  
    const margin = { top: 20, right: 30, bottom: 50, left: 100 };
    const width = 800 - margin.left - margin.right;
    const height = 400 - margin.top - margin.bottom;
  
    onMount(() => {
      d3.csv("df.csv", d => {
        return {
          President: d.President,
          Date: new Date(d.Date),
          GDP: +d.GDP
        };
      }).then(parsedData => {
        data = parsedData;
  
        // Extract unique presidents
        presidents = Array.from(new Set(data.map(d => d.President)));
  
        // Create scales
        x = d3.scaleTime()
          .domain(d3.extent(data, d => d.Date))
          .range([0, width]);
  
        y = d3.scaleBand()
          .domain(presidents)
          .range([0, height])
          .padding(0.1);
  
        // Create axis
        xAxis = d3.axisBottom(x).tickFormat(d3.timeFormat("%Y"));
        yAxis = d3.axisLeft(y);
  
        // Create line generator
        line = d3.line()
          .x(d => x(d.Date))
          .y(d => y(d.President) + y.bandwidth() / 2);
  
        // Render chart
        renderChart();
      });
    });
  
    function renderChart() {
      const svg = d3.select("#chart-container")
        .append("svg")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", "translate(" + margin.left + "," + margin.top + ")");
  
      // Add x-axis
      svg.append("g")
        .attr("class", "x-axis")
        .attr("transform", "translate(0," + height + ")")
        .call(xAxis);
  
      // Add y-axis
      svg.append("g")
        .attr("class", "y-axis")
        .call(yAxis)
        .selectAll("text")
        .style("text-anchor", "end")
        .attr("dx", "-0.5em")
        .attr("dy", "-0.5em")
        .attr("transform", "rotate(-90)");
  
      // Add line
      svg.append("path")
        .datum(data)
        .attr("class", "line")
        .attr("d", line)
        .style("stroke", "#69b3a2")
        .style("stroke-width", 2)
        .style("fill", "none");
    }
  </script>
  
  <style>
    .line {
      fill: none;
      stroke: #69b3a2;
      stroke-width: 2;
    }
  
    .x-axis path,
    .x-axis line,
    .y-axis path,
    .y-axis line {
      fill: none;
      stroke: #ccc;
      shape-rendering: crispEdges;
    }
  
    .x-axis text,
    .y-axis text {
      fill: #333;
      font-size: 12px;
    }
  </style>
  
  <div id="chart-container"></div>