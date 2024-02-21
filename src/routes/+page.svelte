<script>
    import App from '/src/components/map.svelte';

</script>

<h1>US Presidencies impact on the Economy in terms of GDP</h1>
<h3>Created by Joseph Guzman, Daniel Park, and Tracy Pham</h3>
<p> Explore the dynamic shifts in US GDP growth rates over time and across various presidential terms by adjusting the slider. As you move the slider, observe how economic growth fluctuates throughout different administrations. Once you reach the end of the slider, hover over data points on the graph to uncover detailed information about specific dates and growth rates, providing deeper insights into historical economic trends.</p>
<App />

<h4> Visualization Description </h4>
<p>The impact of US presidents on the economy is a topic of significant interest and debate among economists, 
    policymakers, and the public alike. Factors such as the state of the economy when a president takes office, global 
    economic conditions, legislative actions, and unforeseen events all play crucial roles in shaping economic outcomes during a 
    presidency. While it's clear that presidential policies and decisions can influence economic growth and performance, quantifying the precise extent of their impact is complex and often subject to interpretation. 
    <br> <br>
    
    
    In an attempt to answer the question of how change in US presidencies 
    impacts the US economy in terms of GDP, we created an interactive line chart visualization using D3. We used a line chart to visualize the GDP growth rate over time because it effectively 
    shows trends and changes over a continuous period. The x-axis represents time (Month-Year), and the y-axis 
    represents the GDP growth rate in percentage (%). The title, "GDP Growth Rate Over Time", provides a clear
     and concise title for the visualization, indicating the main focus of the chart. An area chart was added 
     below the line chart to emphasize the magnitude of GDP growth rate changes over time. It provides a visual
      representation of the cumulative GDP growth rate. A slider was included allowing users to interactively explore 
      the data by sliding through the years and visually see how as time goes by and the presidency changes, the GDP 
      growth rate also changes significantly. This provides a dynamic way to filter and focus on data for a particular period. As users move the slider, the line and area charts update to show data only up to the selected year along with the president during those years and their political party affliation.For example, if the user moves the slider around 2009 to 2017, which is during the presidency of Barack Obama (Democratic), they will see that there is a significant decline in GDP which gradually recovered throughout Obama’s presidency. Cornflower blue was chosen for the area chart and light sky blue for the line chart to maintain consistency and provide visual appeal without being overly distracting. In addition, we implemented a tooltip feature so when users hover over a data point on the chart, a tooltip dynamically displays the corresponding date and growth rate. This addition improves the user experience by providing contextual information at a glance without cluttering the visualization. Instead of a slider, a dropdown menu could have been used to select a certain president and see how the GDP changed during their term. However, a slider was chosen for its more intuitive and interactive nature, allowing users to visually explore the data by sliding through different years. A line/area chart visualization was chosen because it effectively represents the continuous nature of GDP growth rate data over time and throughout the different presidencies, allowing users to identify trends and patterns easily. Overall, as the user interact with the visualization, they can see that change in presidency does indeed have an impact on the economic state of the country in terms of GDP growth rate.
      <br>  <br> In terms of division of work, we worked together to brainstorm ideas of what we wanted to make a visualization for. Tracy found and did data cleaning on the presidents and their terms dataset while Daniel found and data cleaned the US GDP Growth Rate dataset. While Joseph worked on getting the GitHub website up and running. This initial phase, involving data gathering and preparation, took approximately 2-3 hours of our time. After we got past this portion, we conducted pair programming to create the actual visualization. Initially, we had issues with getting the line chart to render correctly, which proved to be the most time-consuming, taking around 3-4 hours of troubleshooting and refinement. Subsequently, we encountered a setback related to the correct incrementing of time on the x-axis, requiring an additional 2 hours of trying different methods to fix the issue. Once we successfully integrated the slider functionality with the line chart, it was easier to incorporate the presidential data into the chart so that the corresponding presidents and their political party affiliation were displayed alongside the selected years. The decision to incorporate political party affiliation was made towards the end of our development process, as we believed it added an additional layer of context and insight into the economic trends presented in the visualization. We finished off by adding design touches such as centering the visualization, selecting a visually appealing color scheme, refining the text elements, and adding a tooltip. Overall the visualization took us a total of around 12-15 hours.
</p>


