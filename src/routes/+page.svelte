<script>
    import { updated } from '$app/stores';
    import * as d3 from 'd3';
    import * as d3scalechromatic from 'd3-scale-chromatic';
    import { onMount } from 'svelte';

    let svg;
    let xScale;
    let yScale;
    let xAxis;
    let yAxis;

    onMount(() => {
        let margin = {top:50, right:20, left:60, bottom: 50},
        width = 700 - margin.left - margin.right,
        height = 350 - margin.top - margin.bottom;

        svg = d3.select('#my_dataviz')
        .append("svg")
        .attr("viewBox", "0 0 700 350")
        .attr("preserveAspectRatio", "xMinYMin meet")
        .append("g")
        .attr("transform","translate("+margin.left+","+margin.top+")");

        xScale = d3.scaleBand().range([0,width]).padding(0.2);
        yScale = d3.scaleLinear().range([height,0]).nice();

        xAxis = svg.append('g')
        .attr('transform','translate(0,'+height+')');

        yAxis = svg.append('g').attr('class','myYaxis');

        let data;
        d3.csv("/data.csv").then((data) => {
            
            xScale.domain(data.map((d) => d.Month));

            xAxis.transition().duration(1000)
            .call(d3.axisBottom(xScale))

            let values = data.map((d) => d.NoOfEvents);
            yScale.domain([0,Math.max(...values)]);
            
            yAxis.transition().duration(1000)
            .call(d3.axisLeft(yScale))

            var u = svg.selectAll('rect')
            .data(data)

            u.enter()
            .append('rect')
            .merge(u)
            .transition().duration(1000)
            .attr('x', (d) => xScale(d.Month))
            .attr('y', (d) => yScale(d.NoOfEvents))
            .attr('height', (d) => yScale(0)-yScale(d.NoOfEvents))
            .attr('width',xScale.bandwidth())
            .attr('fill','#006699');

        });

        svg.append("text")
        .text("Russia's air/drone strikes on Ukraine become more intense in late 2024")
        .attr("style","font-size:18px; font-weight:900; font-family:Arial")
        .attr("x", -25)
        .attr("y", -20);

        svg.append("text")
        .text("Source: Armed Conflict Location and Event Data (ACLED) | By Pasit Kongkunakornkul")
        .attr("style","font-size:12px; font-family:Arial")
        .attr("x", -25)
        .attr("y", height+40);
    })

</script>

<html>
    <head>
        <title>Test - Pasit Kongkunakornkul</title>
    </head>
    <body>
        <div id="my_dataviz"></div>
    </body>
</html>