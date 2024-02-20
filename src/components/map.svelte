<script> 
    import { onMount } from 'svelte';
    import * as d3 from "d3";
  
    const formatDate = d3.timeFormat("%-b %-d");
  
    let presData = [];
    let selectedYear = '';
  
    // Function to create the visualization
    function createVisualization() {
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
        .tickFormat(formatDate);
  
      const yAxis = d3.axisLeft(yScale);
  
      svg.append("g")
        .attr("transform", `translate(0, ${height})`)
        .call(xAxis);
  
      svg.append("g")
        .call(yAxis);
  
      svg.selectAll(".dot")
        .data(presData)
        .enter().append("circle")
        .attr("class", "dot")
        .attr("cx", d => xScale(d.Date))
        .attr("cy", d => yScale(d.GDP))
        .attr("r", 5)
        .style("fill", "steelblue")
        .append("title")
        .text(d => `${d.President}\nGDP: ${d.GDP}\nDate: ${formatDate(d.Date)}`);
    }
  
    // Create the visualization on component mount
    onMount(() => {
      fetch('df.csv')
        .then(response => response.text())
        .then(csv => {
          presData = d3.csvParse(csv, d3.autoType);
          createVisualization();
        });
    });
  </script>
  
  <svg id="visualization"></svg>
  
  <style>
    .dot {
      cursor: pointer;
    }
  </style>
<!-- 
// const margin = { top: 70, right: 30, bottom: 40, left: 80 };
// const width = 1200 - margin.left - margin.right;
// const height = 500 - margin.top - margin.bottom;

// const x = d3.scaleTime()
//   .range([0, width]);

// const y = d3.scaleLinear()
//   .range([height, 0]);

// const svg = d3.select("#chart-container")
//   .append("svg")
//     .attr("width", width + margin.left + margin.right)
//     .attr("height", height + margin.top + margin.bottom)
//   .append("g")
//     .attr("transform", `translate(${margin.left},${margin.top})`);
// const dataset = [
//   { date: new Date("2022-01-01"), value: 200 },
//   { date: new Date("2022-02-01"), value: 250 },
//   { date: new Date("2022-03-01"), value: 180 },
//   { date: new Date("2022-04-01"), value: 300 },
//   { date: new Date("2022-05-01"), value: 280 },
//   { date: new Date("2022-06-01"), value: 220 },
//   { date: new Date("2022-07-01"), value: 300 },
//   { date: new Date("2022-08-01"), value: 450 },
//   { date: new Date("2022-09-01"), value: 280 },
//   { date: new Date("2022-10-01"), value: 600 },
//   { date: new Date("2022-11-01"), value: 780 },
//   { date: new Date("2022-12-01"), value: 320 }
// ];

// x.domain(d3.extent(dataset, d => d.date));
// y.domain([0, d3.max(dataset, d => d.value)]);

// svg.append("g")
//   .attr("transform", `translate(0,${height})`)
//   .call(d3.axisBottom(x)
//     .ticks(d3.timeMonth.every(1)) 
//     .tickFormat(d3.timeFormat("%b %Y"))); 


// svg.append("g")
//   .call(d3.axisLeft(y));

// const line = d3.line()
//     .x(d => x(d.date))
//     .y(d => y(d.value));

// svg.append("path")
//   .datum(dataset)
//   .attr("fill", "none")
//   .attr("stroke", "steelblue")
//   .attr("stroke-width", 1)
//   .attr("d", line);

// </script> -->
