<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';

  let data = [];
  let svg;
  let xScale, yScale, line, area, avgLine;
  let yDomain; 

  onMount(() => {
    d3.csv('df.csv', d3.autoType).then((csvData) => {
      data = csvData;
      yDomain = [d3.min(data, d => d.GDP), d3.max(data, d => d.GDP)];
      drawPlot(data);
      createSlider(data);
    });
  });

  function drawPlot(data) {
    d3.select('#plot svg').remove();

    const containerWidth = document.getElementById('plot').offsetWidth;
    const containerHeight = 400; 

    const margin = { top: 20, right: 30, bottom: 70, left: 60 }; 
    const width = containerWidth - margin.left - margin.right;
    const height = containerHeight - margin.top - margin.bottom;

    svg = d3.select('#plot')
      .append('svg')
      .attr('width', containerWidth)
      .attr('height', containerHeight)
      .append('g')
      .attr('transform', `translate(${margin.left},${margin.top})`);

    xScale = d3.scaleTime()
      .range([0, width]);

    yScale = d3.scaleLinear()
      .range([height, 0])
      .domain(yDomain); 

    line = d3.line()
      .x(d => xScale(d.Date))
      .y(d => yScale(d.GDP));

    area = d3.area()
      .x(d => xScale(d.Date))
      .y0(height)
      .y1(d => yScale(d.GDP));

    svg.append('g')
      .attr('class', 'x-axis')
      .attr('transform', `translate(0,${height})`);
    
    svg.append('g')
      .attr('class', 'y-axis')
      .call(d3.axisLeft(yScale));

    svg.append('text')
      .attr('class', 'x-axis-label')
      .attr('x', width / 2)
      .attr('y', height + margin.bottom / 2)
      .attr('text-anchor', 'middle')
      .text('Time (Month-Year)');

    svg.append('text')
      .attr('class', 'y-axis-label')
      .attr('transform', 'rotate(-90)')
      .attr('x', -height / 2)
      .attr('y', -margin.left / 1.5)
      .attr('text-anchor', 'middle')
      .text('US GDP Growth Rate Change Over Time (%)');

    svg.append('text')
      .attr('class', 'title')
      .attr('x', width / 2)
      .attr('y', -margin.top / 4)
      .attr('text-anchor', 'middle')
      .style('font-size', '18px')
      .style('font-weight', 'bold')
      .text('How Does US Presidencies impact the Economy in terms of GDP over Time?');

    const tooltip = d3.select('#plot')
      .append('div')
      .attr('class', 'tooltip')
      .style('opacity', 0);

    xScale.domain(d3.extent(data, d => d.Date));

    svg.append('path')
      .datum(data)
      .attr('class', 'area')
      .attr('fill', 'cornflowerblue')
      .attr('d', area);

    svg.append('path')
      .datum(data)
      .attr('class', 'line')
      .attr('fill', 'none')
      .attr('stroke', 'lightskyblue')
      .attr('stroke-width', 1.5)
      .attr('d', line);

    svg.select('.x-axis')
      .call(d3.axisBottom(xScale).tickFormat(d3.timeFormat('%Y')));

    svg.selectAll('.tooltip-dot')
      .data(data)
      .enter()
      .append('circle')
      .attr('class', 'tooltip-dot')
      .attr('cx', d => xScale(d.Date))
      .attr('cy', d => yScale(d.GDP))
      .attr('r', 4) 
      .style('fill', 'transparent')
      .style('stroke', 'none')
      .on('mouseover', function (event, d) {
        const tooltip = d3.select('.tooltip');
        tooltip.transition()
          .duration(200)
          .style('opacity', .9);
        tooltip.html(`<strong>Date:</strong> ${d.Date.toLocaleDateString()}<br><strong>GDP Growth:</strong> ${d.GDP.toFixed(2)}%`) 
          .style('top', (event.pageY - 28) + 'px');
        
        d3.select(this).style('fill', 'rgba(0, 0, 0, 0.3)'); 
      })
      .on('mouseout', function () {
        d3.select('.tooltip')
          .transition()
          .duration(500)
          .style('opacity', 0);
        
        d3.select(this).style('fill', 'transparent'); 
      });
  }

  function createSlider(data) {
    const years = Array.from(new Set(data.map(d => d.Date.getFullYear())));
    const minYear = d3.min(years);
    const maxYear = d3.max(years);

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

        const presidents = getPresidentsForYear(filteredData, selectedYear);
        updatePresidentText(presidents);
      });
  }

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

  function getPresidentsForYear(data, year) {
    const presidents = data.filter(d => d.Date.getFullYear() === year);
    return presidents.length > 0 ? presidents.map(d => `${d.President} (${d.Party})`).join(', ') : 'Unknown';
  }

  function updatePresidentText(presidents) {
    d3.select('.president-text').text(`President and Party: ${presidents}`);
  }
</script>

<style>


  #container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 60vh; 
  }

  #plot {
    width: 80%; 
    border-radius: 5px;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  }

  svg {
    max-width: 100%;
    height: auto;
  }
</style>

<div id="container">
  <div id="plot">
  </div>
</div>
<div id="slider"></div>
<div class="president-text"></div>
