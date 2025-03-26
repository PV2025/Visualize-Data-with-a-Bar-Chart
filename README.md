# Visualize-Data-with-a-Bar-Chart

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Bar Chart Visualization</title>
  <script src="https://d3js.org/d3.v7.min.js"></script>
  <script src="https://cdn.freecodecamp.org/testable-projects-fcc/v1/bundle.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
    }
    svg {
      background-color: #f9f9f9;
    }
    .bar {
      fill: steelblue;
    }
    #tooltip {
      position: absolute;
      background: rgba(0, 0, 0, 0.8);
      color: white;
      padding: 6px;
      border-radius: 4px;
      font-size: 0.8em;
      pointer-events: none;
      visibility: hidden;
    }
  </style>
</head>
<body>
  <h1 id="title">United States GDP</h1>
  <div id="tooltip"></div>
  <svg width="960" height="500"></svg>

  <script>
    const svg = d3.select("svg"),
          margin = {top: 50, right: 30, bottom: 50, left: 60},
          width = +svg.attr("width") - margin.left - margin.right,
          height = +svg.attr("height") - margin.top - margin.bottom,
          chart = svg.append("g").attr("transform", `translate(${margin.left},${margin.top})`);

    const tooltip = d3.select("#tooltip");

    const url = "https://raw.githubusercontent.com/freeCodeCamp/ProjectReferenceData/master/GDP-data.json";

    d3.json(url).then(data => {
      const dataset = data.data;

      const x = d3.scaleTime()
                  .domain([new Date(dataset[0][0]), new Date(dataset[dataset.length - 1][0])])
                  .range([0, width]);

      const y = d3.scaleLinear()
                  .domain([0, d3.max(dataset, d => d[1])])
                  .range([height, 0]);

      const xAxis = d3.axisBottom(x);
      const yAxis = d3.axisLeft(y);

      chart.append("g")
           .attr("id", "x-axis")
           .attr("transform", `translate(0,${height})`)
           .call(xAxis);

      chart.append("g")
           .attr("id", "y-axis")
           .call(yAxis);

      const barWidth = width / dataset.length;

      chart.selectAll(".bar")
           .data(dataset)
           .enter()
           .append("rect")
           .attr("class", "bar")
           .attr("x", d => x(new Date(d[0])))
           .attr("y", d => y(d[1]))
           .attr("width", barWidth)
           .attr("height", d => height - y(d[1]))
           .attr("data-date", d => d[0])
           .attr("data-gdp", d => d[1])
           .on("mouseover", (event, d) => {
              tooltip.style("visibility", "visible")
                     .attr("data-date", d[0])
                     .html(`<strong>Date:</strong> ${d[0]}<br><strong>GDP:</strong> $${d[1]} Billion`)
                     .style("left", event.pageX + 10 + "px")
                     .style("top", event.pageY - 30 + "px");
           })
           .on("mouseout", () => tooltip.style("visibility", "hidden"));
    });
  </script>
</body>
</html>
