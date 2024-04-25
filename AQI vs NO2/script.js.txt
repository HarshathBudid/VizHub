// Chart dimensions
const width = 700; // Increased width for more space
const height = 500; // Increased height to accommodate title
const margin = {
  top: 40,
  right: 180,
  bottom: 60,
  left: 60,
}; // Increased top margin for title
const chartWidth = width - margin.left - margin.right;
const chartHeight = height - margin.top - margin.bottom;

// Create SVG container
const svg = d3
  .select('#chart')
  .append('svg')
  .attr('width', width) // Updated width
  .attr('height', height); // Updated height

// Add title to the center
svg
  .append('text')
  .attr('x', width / 2) // Center horizontally
  .attr('y', 20) // Adjusted y-position for title
  .attr('text-anchor', 'middle') // Align text to center
  .attr('font-size', '18px') // Title font size
  .attr('font-weight', 'bold') // Bold font for emphasis
  .text('AQI value vs NO2 AQI Value'); // Chart title

// Create chart group
const chart = svg
  .append('g')
  .attr(
    'transform',
    `translate(${margin.left}, ${margin.top})`,
  );

// Country colors for the scatter plot
const countryColors = {
  India: 'green',
  'United States of America': 'blue',
  Australia: 'yellow',
  Brazil: 'orange',
  Canada: 'purple',
  China: 'black',
  'Republic of Congo': 'lightblue',
  Mexico: 'lavender',
  Pakistan: 'pink',
  'United Kingdom': 'red',
};

// Read the CSV file
d3.csv(
  'https://gist.githubusercontent.com/HarshathBudid/9ccc0223e27e29a127a0884eacdb7a97/raw/cf4305cb27f8a5a1a140cbf5ad1c41cfd65358aa/AQI.csv',
).then((data) => {
  // Parse numeric values
  data.forEach((d) => {
    d.AQI_Value = +d['AQI Value'];
    d.NO2_AQI_Value = +d['NO2 AQI Value'];
  });

  // Create scales
  const xScale = d3
    .scaleLinear()
    .domain([0, d3.max(data, (d) => d.NO2_AQI_Value)])
    .range([0, chartWidth]);

  const yScale = d3
    .scaleLinear()
    .domain([0, d3.max(data, (d) => d.AQI_Value)])
    .range([chartHeight, 0]);

  // Create axes with labels
  const xAxis = d3.axisBottom(xScale).ticks(5);
  const yAxis = d3.axisLeft(yScale).ticks(5);

  chart
    .append('g')
    .attr('transform', `translate(0, ${chartHeight})`)
    .call(xAxis);

  // X-axis label
  chart
    .append('text')
    .attr('x', chartWidth / 2) // Centered on x-axis
    .attr('y', chartHeight + margin.bottom / 2) // Below x-axis
    .attr('text-anchor', 'middle')
    .attr('fill', 'black')
    .text('NO2 AQI Value'); // Label for x-axis

  chart.append('g').call(yAxis);

  // Y-axis label
  chart
    .append('text')
    .attr('x', -chartHeight / 2) // Centered along y-axis
    .attr('y', -margin.left / 2) // Above y-axis
    .attr('transform', 'rotate(-90)') // Rotated to align vertically
    .attr('text-anchor', 'middle')
    .attr('fill', 'black')
    .text('AQI Value'); // Label for y-axis

  // Create scatter plot circles with country-based colors
  chart
    .selectAll('circle')
    .data(data)
    .enter()
    .append('circle')
    .attr('cx', (d) => xScale(d.NO2_AQI_Value)) // X-axis position
    .attr('cy', (d) => yScale(d.AQI_Value)) // Y-axis position
    .attr('r', 5)
    .attr('fill', (d) => countryColors[d.Country]); // Circle color based on country

  // Add a simple legend
  const legendData = Object.entries(countryColors);

  const legend = svg
    .append('g')
    .attr(
      'transform',
      `translate(${width - margin.right + 20}, ${margin.top})`,
    ); // Adjusted position

  legend
    .selectAll('rect')
    .data(legendData)
    .enter()
    .append('rect')
    .attr('x', 0)
    .attr('y', (d, i) => i * 30) // Spacing between legend items
    .attr('width', 15) // Rectangle width
    .attr('height', 15) // Rectangle height
    .attr('fill', (d) => d[1]); // Fill color

  legend
    .selectAll('text')
    .data(legendData)
    .enter()
    .append('text')
    .attr('x', 25) // Position relative to the rectangle
    .attr('y', (d, i) => i * 30 + 12) // Align text with rectangles
    .attr('font-size', '11px') // Smaller font for longer labels
    .attr('fill', 'black')
    .text((d) => d[0]); // Legend labels
});
