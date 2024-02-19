<h1>Welcome to SvelteKit</h1>
<p>Visit <a href="https://kit.svelte.dev">kit.svelte.dev</a> to read the documentation</p>

<script> 
import * as d3 from "d3";
import Charts from "./Chart/Chart.svelte";
import Bars from "./Chart/Bars.svelte";
import Axis from "./ChartAxis.svelte";

const formatDate = d3.timeFormate("%-b %-d")
export let data = [];
//export let xAcessor = d => d.x;
export let label;

</script>