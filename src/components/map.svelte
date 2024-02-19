<script> 
import * as d3 from "d3";
import { onMount } from 'svelte';

const formatDate = d3.timeFormat("%-b %-d");

let presData = [];
let gdpData = [];

 onMount(async () => {

     const res = await fetch('pres.csv'); 

     const csv = await res.text();

     presData = d3.csvParse(csv, d3.autoType)

     console.log(presData);

 });

 onMount(async () => {

const res = await fetch('world_GDP.csv'); 

const csv = await res.text();

gdpData = d3.csvParse(csv, d3.autoType)

console.log(gdpData);

});
const margin = { top: 70, right: 30, bottom: 40, left: 80};
const width = 1200 - margin.left - margin.right;
const height = 500 - margin.top - margin.bottom;


</script>
