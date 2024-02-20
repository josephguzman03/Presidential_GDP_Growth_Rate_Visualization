<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  import data from './df.csv';

  let gdpData = [];
  let presidents = [];
  let currentPresident = '';
  let currentYear = getMinYear(); // Initialize to the minimum year

  // Read CSV data
  onMount(async () => {
    gdpData = processData(data);
    presidents = gdpData.map(d => d.President);
    updateVisualization(); // Initial visualization
  });

  // Process CSV data
  function processData(data) {
    return data.map(d => ({
      President: d.President,
      Date: new Date(d.Date),
      GDP: +d.GDP
    }));
  }

  // Update current president based on slider position
  function updateCurrentPresident(year) {
    const presidentData = gdpData.find(d => d.Date.getFullYear() === year);
    currentPresident = presidentData ? presidentData.President : '';
  }

  // D3 visualization
  function updateVisualization() {
    const margin = { top: 20, right: 30, bottom: 30, left: 40 };
    const width = 600 - margin.left - margin.right;
    const height = 400 - margin.top - margin.bottom;

    const svg = d3.select("#visualization")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
      .append("g")
      .attr("transform", `translate(${margin.left},${margin.top})`);

    const x = d3.scaleTime()
      .domain(d3.extent(gdpData, d => d.Date))
      .range([0, width]);

    const y = d3.scaleLinear()
      .domain([0, d3.max(gdpData, d => d.GDP)])
      .nice()
      .range([height, 0]);

    const line = d3.line()
      .x(d => x(d.Date))
      .y(d => y(d.GDP));

    svg.append("g")
      .attr("class", "x-axis")
      .attr("transform", `translate(0,${height})`)
      .call(d3.axisBottom(x));

    svg.append("g")
      .attr("class", "y-axis")
      .call(d3.axisLeft(y));

    svg.append("path")
      .datum(gdpData)
      .attr("fill", "none")
      .attr("stroke", "steelblue")
      .attr("stroke-width", 1.5)
      .attr("d", line);

    updateCurrentPresident(currentYear);
  }

  // Get the minimum year
  function getMinYear() {
    return d3.min(gdpData, d => d.Date.getFullYear());
  }

  // Get the maximum year
  function getMaxYear() {
    return d3.max(gdpData, d => d.Date.getFullYear());
  }

  // Update visualization when slider changes
  function updateYear(event) {
    currentYear = +event.target.value;
    updateVisualization();
  }
</script>

<div>
  <svg id="visualization"></svg>
  <input type="range" min={getMinYear()} max={getMaxYear()} bind:value={currentYear} on:input={updateYear}>
  <p>Current President: {currentPresident}</p>
</div>

<!-- <style>
  /* Add your CSS styles here */
</style> -->