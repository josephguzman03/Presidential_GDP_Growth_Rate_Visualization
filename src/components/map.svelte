<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  // Define your data
  let data = [];

  // Load data from CSV on component mount
  onMount(() => {
    d3.csv('df.csv', d3.autoType).then((csvData) => {
      data = csvData;
      drawPlot(data);
    });
  });

  // Function to draw the plot
  function drawPlot(data) {
    // Define dimensions and margins
    const width = 600;
    const height = 400;
    const margin = { top: 20, right: 30, bottom: 60, left: 60 };

    // Remove existing plot if it exists
    d3.select('#plot').selectAll('*').remove();

    // Create SVG
    const svg = d3.select('#plot')
      .append('svg')
      .attr('width', width + margin.left + margin.right)
      .attr('height', height + margin.top + margin.bottom)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    // Get the selected president from the dropdown
    const selectedPresident = d3.select('#dropdown select').node().value;

    // Filter data for the selected president
    const filteredData = data.filter(d => d.President === selectedPresident);

    // Parse dates
    const parseDate = d3.timeParse('%Y-%m-%d');
    filteredData.forEach(d => d.Date = parseDate(d.Date));

    // Define scales
    const xScale = d3.scaleTime()
      .domain(d3.extent(filteredData, d => d.Date))
      .range([0, width]);

    const yScale = d3.scaleLinear()
      .domain([0, d3.max(filteredData, d => d.GDP)])
      .range([height, 0]);

    // Define line function
    const line = d3.line()
      .x(d => xScale(d.Date))
      .y(d => yScale(d.GDP));

    // Draw line
    svg.append('path')
      .datum(filteredData)
      .attr('fill', 'none')
      .attr('stroke', 'steelblue')
      .attr('stroke-width', 1.5)
      .attr('d', line);

    // Draw x-axis
    svg.append('g')
      .attr('transform', `translate(0,${height})`)
      .call(d3.axisBottom(xScale)
        .tickFormat(d3.timeFormat('%b %Y'))); // Format ticks as 'Month Year'

    // Draw y-axis
    svg.append('g')
      .call(d3.axisLeft(yScale));

    // Define dropdown options
    const options = [...new Set(data.map(d => d.President))];

    // Create dropdown
    const dropdown = d3.select('#dropdown')
      .append('select');

    dropdown.selectAll('option')
      .data(options)
      .enter()
      .append('option')
      .text(d => d)
      .attr('value', d => d)
      .property('selected', d => d === options[0]) // Set first option as selected by default

    // Add event listener to dropdown
    dropdown.on('change', function() {
      drawPlot(data); // Redraw plot when dropdown value changes
    });
  }
</script>

<style>
  /* Define styles if needed */
</style>

<div id="plot"></div>
<div id="dropdown"></div>
