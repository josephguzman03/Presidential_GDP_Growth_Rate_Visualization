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
        .attr("class", "x-axis")
        .attr("transform", `translate(0, ${height})`)
        .call(xAxis);
  
      svg.append("g")
        .attr("class", "y-axis")
        .call(yAxis);
  
      // Highlight President's terms
      svg.selectAll(".term")
        .data(presData)
        .enter().append("rect")
        .attr("class", "term")
        .attr("x", d => xScale(d.Date))
        .attr("y", 0)
        .attr("width", (d, i) => {
          if (i < presData.length - 1) {
            return xScale(presData[i + 1].Date) - xScale(d.Date);
          } else {
            return xScale.range()[1] - xScale(d.Date);
          }
        })
        .attr("height", height)
        .attr("fill", (d, i) => i % 2 === 0 ? "rgba(0, 0, 0, 0.1)" : "rgba(0, 0, 0, 0)")
        .on("mouseover", (event, d) => {
          d3.select(event.currentTarget)
            .attr("fill", "rgba(0, 0, 0, 0.2)");
        })
        .on("mouseout", (event, d) => {
          d3.select(event.currentTarget)
            .attr("fill", (d, i) => i % 2 === 0 ? "rgba(0, 0, 0, 0.1)" : "rgba(0, 0, 0, 0)");
        });
  
      // Highlight GDP data points
      svg.selectAll(".dot")
        .data(presData)
        .enter().append("circle")
        .attr("class", "dot")
        .attr("cx", d => xScale(d.Date))
        .attr("cy", d => yScale(d.GDP))
        .attr("r", 5)
        .style("fill", "steelblue")
        .on("mouseover", (event, d) => {
          d3.select(event.currentTarget)
            .attr("r", 8);
        })
        .on("mouseout", (event, d) => {
          d3.select(event.currentTarget)
            .attr("r", 5);
        });
  
      // Update x-axis and dots positions based on selected year
      const slider = document.getElementById("slider");
      slider.addEventListener("input", () => {
        const index = parseInt(slider.value);
        updateVisualization(presData[index]);
      });
  
      function updateVisualization(selectedData) {
        const xAxis = d3.axisBottom(xScale)
          .tickFormat(formatTime);
  
        svg.select(".x-axis")
          .call(xAxis);
  
        svg.selectAll(".dot")
          .attr("cx", d => xScale(d.Date))
          .attr("cy", d => yScale(d.GDP));
  
        svg.selectAll(".term")
          .attr("x", d => xScale(d.Date))
          .attr("width", (d, i) => {
            if (i < presData.length - 1) {
              return xScale(presData[i + 1].Date) - xScale(d.Date);
            } else {
              return xScale.range()[1] - xScale(d.Date);
            }
          });
      }
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
  
  <input type="range" id="slider" min="0" max="{presData.length - 1}" value="0">
  
  <style>
    .dot {
      cursor: pointer;
    }
  
    .term {
      cursor: pointer;
    }
  </style>