<script> 
import App from '../components/App.svelte';
import * as d3 from "d3";
import Charts from "./Chart/Chart.svelte";
import Bars from "./Chart/Bars.svelte";
import Axis from "./ChartAxis.svelte";

const formatDate = d3.timeFormat("%-b %-d");

function main() {
    d3.csv('src/components/pres.csv', d3.autoType).then(
        function(d) {
            for (let index = 0; index < d.length; index++) {
                const element = d[index];
                console.log(element.President);
                console.log(element.Start);
            }
        }
    )    
}

const margin = { top: 70, right: 30, bottom: 40, left: 80};
const width = 1200 - margin.left - margin.right;
const height = 500 - margin.top - margin.bottom;

// const x = d3.scaletime().range([0, width]);
// const y = d3.scaleLinear().range([height, 0]);

// const svg = d3.select("#chart-container")
//     .append("svg")
//         .attr("width", width + margin.left + margin.right)
//         .attr("height", height + margin.top + margin.bottoms)
//     .append("g")


</script>
