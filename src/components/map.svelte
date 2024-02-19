<script> 
import App from '../components/App.svelte';
import * as d3 from "d3";
import Charts from "./Chart/Chart.svelte";
import Bars from "./Chart/Bars.svelte";
import Axis from "./ChartAxis.svelte";

const formatDate = d3.timeFormat("%-b %-d");

let presData = [];

 onMount(async () => {

     const res = await fetch('pres.csv'); 

     const csv = await res.text();

     tempData = d3.csvParse(csv, d3.autoType)

     console.log(presData);

 });

const margin = { top: 70, right: 30, bottom: 40, left: 80};
const width = 1200 - margin.left - margin.right;
const height = 500 - margin.top - margin.bottom;


</script>
