<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    let data = []; // Store the parsed data
    let xScale, yScale; // Scales for x-axis and y-axis
    let svg; // SVG element for the visualization
    let slider; // Slider element for selecting the year
  
    // Load data from df.csv and create the visualization
    onMount(() => {
      d3.csv("df.csv", row => {
        return {
          President: row.President,
          Date: new Date(row.Date), // Convert Date to JavaScript Date object
          GDP: +row.GDP // Convert GDP to number
        };
      }).then(parsedData => {
        data = parsedData;
  
        // Create scales for x-axis (time) and y-axis (GDP)
        const minYear = d3.min(data, d => d.Date.getFullYear());
        const maxYear = d3.max(data, d => d.Date.getFullYear());
        xScale = d3.scaleLinear().domain([minYear, maxYear]).range([0, 800]);
        yScale = d3.scaleLinear().domain([0, d3.max(data, d => d.GDP)]).range([400, 0]);
  
        // Create SVG element
        svg = d3.select("#visualization")
          .append("svg")
          .attr("width", 800)
          .attr("height", 400);
  
        // Add x-axis
        svg.append("g")
          .attr("transform", "translate(0, 400)")
          .call(d3.axisBottom(xScale));
  
        // Add y-axis
        svg.append("g")
          .call(d3.axisLeft(yScale));
  
        // Add slider for selecting the year
        slider = d3.select("#slider")
          .attr("min", minYear)
          .attr("max", maxYear)
          .attr("value", minYear)
          .on("input", updateVisualization);
  
        // Initial visualization
        updateVisualization();
      });
    });
  
    // Function to update the visualization based on the selected year
    function updateVisualization() {
      const selectedYear = slider.property("value");
      const filteredData = data.filter(d => d.Date.getFullYear() === +selectedYear);
  
      // Update visualization
      const circles = svg.selectAll("circle").data(filteredData);
  
      circles.enter().append("circle")
        .attr("cx", d => xScale(d.Date.getFullYear()))
        .attr("cy", d => yScale(d.GDP))
        .attr("r", 5)
        .attr("fill", "steelblue")
        .merge(circles)
        .transition()
        .duration(500)
        .attr("cx", d => xScale(d.Date.getFullYear()))
        .attr("cy", d => yScale(d.GDP));
  
      circles.exit().remove();
    }
  </script>
  
  <div id="visualization"></div>
  <input type="range" id="slider" />