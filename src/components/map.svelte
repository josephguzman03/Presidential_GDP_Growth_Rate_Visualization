<script>
  // Import necessary functions and modules
  import { onMount } from 'svelte';
  import * as d3 from 'd3';
  
  // Define variables to store data and state
  let gdpData = []; // Data from the CSV file
  let presidents = []; // List of presidents
  let currentPresident = ''; // Name of the current president based on the slider position
  let currentYear = getMinYear(); // Initialize the slider to the minimum year
  
  onMount(async () => {
    const res = await fetch('df.csv'); 
    const csv = await res.text();
    tempData = d3.csvParse(csv, d3.autoType)
    onsole.log(tempData);

});
//   // Function to load CSV data and initialize visualization
//   onMount(async () => {
//     // Load data from the CSV file
//     const response = await fetch('df.csv');
//     const csvData = await response.text();
//     gdpData = processData(csvData); // Process the CSV data
//     presidents = [...new Set(gdpData.map(d => d.President))]; // Extract unique president names
//     updateVisualization(); // Initialize the visualization
//   });

//   // Function to process CSV data
//   function processData(csvData) {
//     // Parse the CSV data using d3.csvParse
//     return d3.csvParse(csvData, d => ({
//       President: d.President,
//       Date: new Date(d.Date),
//       GDP: +d.GDP
//     }));
//   }

//   // Function to update the current president based on the slider position
//   function updateCurrentPresident(year) {
//     // Find the president who was in office during the specified year
//     const presidentData = gdpData.find(d => d.Date.getFullYear() === year);
//     // Update the current president variable
//     currentPresident = presidentData ? presidentData.President : '';
//   }

//   // Function to create or update the D3 visualization
//   function updateVisualization() {
//     // Define dimensions and margins for the SVG container
//     const margin = { top: 20, right: 30, bottom: 30, left: 40 };
//     const width = 600 - margin.left - margin.right;
//     const height = 400 - margin.top - margin.bottom;

//     // Select the SVG container and set its width and height
//     const svg = d3.select("#visualization")
//       .attr("width", width + margin.left + margin.right)
//       .attr("height", height + margin.top + margin.bottom)
//       .append("g")
//       .attr("transform", `translate(${margin.left},${margin.top})`);

//     // Define scales for x and y axes
//     const x = d3.scaleTime()
//       .domain(d3.extent(gdpData, d => d.Date))
//       .range([0, width]);

//     const y = d3.scaleLinear()
//       .domain([0, d3.max(gdpData, d => d.GDP)])
//       .nice()
//       .range([height, 0]);

//     // Define line function to draw the line chart
//     const line = d3.line()
//       .x(d => x(d.Date))
//       .y(d => y(d.GDP));

//     // Append x-axis to the SVG container
//     svg.append("g")
//       .attr("class", "x-axis")
//       .attr("transform", `translate(0,${height})`)
//       .call(d3.axisBottom(x));

//     // Append y-axis to the SVG container
//     svg.append("g")
//       .attr("class", "y-axis")
//       .call(d3.axisLeft(y));

//     // Append the line chart to the SVG container
//     svg.append("path")
//       .datum(gdpData)
//       .attr("fill", "none")
//       .attr("stroke", "steelblue")
//       .attr("stroke-width", 1.5)
//       .attr("d", line);

//     // Update the current president based on the initial slider position
//     updateCurrentPresident(currentYear);
//   }

//   // Function to get the minimum year from the data
//   function getMinYear() {
//     return d3.min(gdpData, d => d.Date.getFullYear());
//   }

//   // Function to get the maximum year from the data
//   function getMaxYear() {
//     return d3.max(gdpData, d => d.Date.getFullYear());
//   }

//   // Function to update the visualization when the slider value changes
//   function updateYear(event) {
//     // Update the current year based on the slider value
//     currentYear = +event.target.value;
//     // Update the visualization with the new slider value
//     updateVisualization();
//   }
</script>
