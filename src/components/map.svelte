<script>
  // Import necessary modules
  import { onMount } from 'svelte';
  import * as d3 from 'd3';


  // Define your data
  let data = [];
  let svg;
  let xScale, yScale, line, area, avgLine;
  let yDomain; // Define yScale domain

  // Load data from CSV on component mount
  onMount(() => {
    d3.csv('df.csv', d3.autoType).then((csvData) => {
      data = csvData;
      // Set initial yScale domain
      yDomain = [d3.min(data, d => d.GDP), d3.max(data, d => d.GDP)];
      drawPlot(data);
      createSlider(data);
    });
  });

  // Function to draw the plot
  function drawPlot(data) {
  // Define dimensions and margins
  const width = 600;
  const height = 400;
  const margin = { top: 20, right: 30, bottom: 70, left: 60 }; // Adjust left margin to accommodate y-axis label

  // Create SVG if it doesn't exist
  if (!svg) {
    // Create SVG
    svg = d3.select('#plot')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    // Define scales
    xScale = d3.scaleTime()
      .range([0, width]);

    yScale = d3.scaleLinear()
      .range([height, 0])
      .domain(yDomain); // Set initial yScale domain

    // Define line function
    line = d3.line()
      .x(d => xScale(d.Date))
      .y(d => yScale(d.GDP));

    // Define area function
    area = d3.area()
      .x(d => xScale(d.Date))
      .y0(height)
      .y1(d => yScale(d.GDP));

    // Draw x-axis
    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`);
    
    // Draw y-axis
    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(yScale));

    // Add x-axis label
    svg.append('text')
      .attr('class', 'x-axis-label')
      .attr('x', width / 2)
      .attr('y', height + margin.bottom / 2)
      .attr('text-anchor', 'middle')
      .text('Time (Month-Year)');

    // Add y-axis label
    svg.append('text')
      .attr('class', 'y-axis-label')
      .attr('transform', 'rotate(-90)')
      .attr('x', -height / 2)
      .attr('y', -margin.left / 1.5)
      .attr('text-anchor', 'middle')
      .text('US GDP Growth Rate Change Over Time (%)');

    // Add title
    svg.append('text')
      .attr('class', 'title')
      .attr('x', width / 2)
      .attr('y', -margin.top / 4)
      .attr('text-anchor', 'middle')
      .style('font-size', '18px')
      .style('font-weight', 'bold')
      .text('GDP Growth Rate Over Time');

    // Append tooltip div
    const tooltip = d3.select('#plot')
      .append('div')
      .attr('class', 'tooltip')
      .style('opacity', 0);
  }

  // Update scales domain
  xScale.domain(d3.extent(data, d => d.Date));

  // Select existing plot area
  const plotArea = svg.selectAll('.plot-area');

  // If plot area doesn't exist, create it
  if (plotArea.empty()) {
    // Draw area
    svg.append('path')
      .attr('class', 'area')
      .attr('fill', 'cornflowerblue');

    // Draw line
    svg.append('path')
      .attr('class', 'line')
      .attr('fill', 'none')
      .attr('stroke', 'lightskyblue')
      .attr('stroke-width', 1.5);
  }

  // Update area path
  svg.select('.area')
    .datum(data)
    .attr('d', area);

  // Update line path
  svg.select('.line')
    .datum(data)
    .attr('d', line);

  // Update x-axis
  svg.select('.x-axis')
    .call(d3.axisBottom(xScale).tickFormat(d3.timeFormat('%Y')));

  // Add interaction - tooltip
  svg.selectAll('.tooltip').remove(); // Remove existing tooltips

  svg.selectAll('.tooltip-dot')
    .data(data)
    .enter()
    .append('circle')
    .attr('class', 'tooltip-dot')
    .attr('cx', d => xScale(d.Date))
    .attr('cy', d => yScale(d.GDP))
    .attr('r', 4) // Set the radius of the circle
    .style('fill', 'transparent')
    .style('stroke', 'none')
    .on('mouseover', function (event, d) {
      const tooltip = d3.select('.tooltip');
      tooltip.transition()
        .duration(200)
        .style('opacity', .9);
      tooltip.html(`<strong>Date:</strong> ${d.Date.toLocaleDateString()}<br><strong>GDP Growth:</strong> ${d.GDP.toFixed(2)}%`) // Show the date, GDP growth, and any other relevant value
        .style('left', (event.pageX + 10) + 'px')
        .style('top', (event.pageY - 28) + 'px');
      
      d3.select(this).style('fill', 'rgba(0, 0, 0, 0.3)'); // Set the fill color and opacity for the dot
    })
    .on('mouseout', function () {
      d3.select('.tooltip')
        .transition()
        .duration(500)
        .style('opacity', 0);
      
      d3.select(this).style('fill', 'transparent'); // Set the fill color back to transparent on mouseout
    });

}
  // Function to create the slider
  function createSlider(data) {
    // Define slider range
    const years = Array.from(new Set(data.map(d => d.Date.getFullYear())));
    const minYear = d3.min(years);
    const maxYear = d3.max(years);

    // Create slider
    const slider = d3.select('#slider')
      .append('input')
      .attr('type', 'range')
      .attr('min', minYear)
      .attr('max', maxYear)
      .attr('step', '1')
      .on('input', function() {
        const selectedYear = +this.value;
        const filteredData = data.filter(d => d.Date.getFullYear() <= selectedYear);
        drawPlot(filteredData);

        // Get the presidents for the selected year
        const presidents = getPresidentsForYear(filteredData, selectedYear);
        updatePresidentText(presidents);
      });
  }

  // Function to calculate average GDP growth per presidential term
  function calculateAverageGrowth(data) {
    const avgData = [];
    let sum = 0;
    let count = 0;
    let startYear = data[0].Date.getFullYear();

    data.forEach((d, i) => {
      sum += d.GDP;
      count++;
      if (i === data.length - 1 || data[i + 1].Date.getFullYear() - startYear >= 4) {
        avgData.push({
          start: new Date(startYear, 0, 1),
          average: sum / count
        });
        sum = 0;
        count = 0;
        startYear = data[i + 1] ? data[i + 1].Date.getFullYear() : startYear + 4;
      }
    });

    return avgData;
  }

    // Function to get the presidents for a given year
    function getPresidentsForYear(data, year) {
    const presidents = data.filter(d => d.Date.getFullYear() === year);
    return presidents.length > 0 ? presidents.map(d => `${d.President} (${d.Party})`).join(', ') : 'Unknown';
  }

  // Function to update the president text element
    function updatePresidentText(presidents) {
    d3.select('.president-text').text(`President and Party: ${presidents}`);
  }
</script>

<style>
  /* Define styles */

  #container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 60h; /* Adjust the height as needed */
  }

  #plot {
    max-width: 80%; /* Adjust the max-width as needed */
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); /* Adding box shadow */
  }

  .chart-svg {
    font-family: 'Arial', sans-serif;
    background-color: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 5px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  }

  .axis text {
    font-size: 12px;
    fill: #777;
  }

  .axis path,
  .axis line {
    fill: none;
    stroke: #ddd;
    shape-rendering: crispEdges;
  }

  .area {
    fill: cornflowerblue;
    fill-opacity: 0.3;
    stroke: none;
  }

  .line {
    fill: none;
    stroke: lightskyblue;
    stroke-width: 1.5;
  }

  .avg-line {
    fill: none;
    stroke: orange;
    stroke-width: 1.5;
    stroke-dasharray: 3, 3;
  }

  .slider {
    margin-top: 20px;
    margin-bottom: center;
    width: 80%;
  }

  /* Tooltip styles */
  .tooltip {
    position: absolute;
    background-color: #f9f9f9;
    border: 1px solid #ddd;
    border-radius: 5px;
    padding: 8px;
    pointer-events: none;
    opacity: 0;
  }
</style>

<div id="container">
  <div id="plot">
    <!-- Plot SVG and other elements will be rendered here -->
  </div>
</div>
<div id="slider"></div>
<!-- President Text -->
<div class="president-text"></div>