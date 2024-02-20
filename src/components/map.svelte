<script>
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
  
    const formatTime = d3.timeFormat("%Y");
  
    let presData = [];
    let selectedYear = '';
  
    // Function to create the visualization
    function createVisualization(selectedData) {
      const margin = { top: 20, right: 30, bottom: 50, left: 50 };
      const width = 800 - margin.left - margin.right;
      const height = 400 - margin.top - margin.bottom;
  
      const svg = d3.select("#visualization")
        .attr("width", width + margin.left + margin.right)
        .attr("height", height + margin.top + margin.bottom)
        .append("g")
        .attr("transform", `translate(${margin.left}, ${margin.top})`);
  
      const xScale = d3.scaleTime()
        .domain(d3.extent(presData, d => d.Date))
        .range([0, width]);
  
      const yScale = d3.scaleLinear()
        .domain([0, d3.max(presData, d => d.GDP)])
        .range([height, 0]);
  
      const xAxis = d3.axisBottom(xScale)
        .tickFormat(formatTime);
  
      const yAxis = d3.axisLeft(yScale);
  
      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(xAxis);
  
      svg.append("g")
        .call(yAxis);
  
      const filteredData = presData.filter(d => formatTime(d.Date) === formatTime(selectedData.Date));
  
      svg.selectAll(".dot")
        .data(filteredData)
        .enter().append("circle")
        .attr("class", "dot")
        .attr("cx", d => xScale(d.Date))
        .attr("cy", d => yScale(d.GDP))
        .attr("r", 5)
        .style("fill", "steelblue")
        .append("title")
        .text(d => `${d.President}\nGDP: ${d.GDP}\nDate: ${formatTime(d.Date)}`);
    }
  
    // Create the visualization on component mount
    onMount(() => {
      fetch('df.csv')
        .then(response => response.text())
        .then(csv => {
          presData = d3.csvParse(csv, d3.autoType);
          createVisualization(presData[0]);
        });
    });
  </script>
  
  <svg id="visualization"></svg>
  
  <div class="button-container">
    {#each presData as data}
      <button on:click={() => createVisualization(data)}>{data.President} - {formatTime(data.Date)}</button>
    {/each}
  </div>
  
  <style>
    .dot {
      cursor: pointer;
    }
    .button-container {
      display: flex;
      gap: 5px;
      padding: 10px;
      overflow-x: auto;
    }
    .button-container button {
      cursor: pointer;
      padding: 5px 10px;
      border-radius: 5px;
      border: none;
      background-color: #ddd;
      transition: background-color 0.3s ease;
    }
    .button-container button:hover {
      background-color: #bbb;
    }
  </style>
