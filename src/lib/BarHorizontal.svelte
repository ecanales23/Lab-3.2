<script>
import * as d3 from 'd3';

let width = 500;
let height = 300;
export let data = [];

let margin = { top: 40, right: 160, bottom: 50, left: 80 };
let innerWidth  = width  - margin.left - margin.right;
let innerHeight = height - margin.top  - margin.bottom;
let xAxis, yAxis;

// xScale is now linear (quantitative)
$: xScale = d3.scaleLinear()
    .domain([0, d3.max(data, d => d.value) || 1])
    .range([0, innerWidth]);

// yScale is now band (categorical)
$: yScale = d3.scaleBand()
    .domain(data.map(d => d.label))
    .range([0, innerHeight])
    .padding(0.2);

$: colorScale = d3.scaleOrdinal(d3.schemeTableau10)
    .domain(data.map(d => d.label));

$: if (xAxis && yAxis) {
    d3.select(xAxis).call(d3.axisBottom(xScale).ticks(5));
    d3.select(yAxis).call(d3.axisLeft(yScale));
}

$: maxBar = d3.greatest(data, d => d.value);
</script>

<div class="container">
    <svg viewBox="0 0 {width} {height}">
        <!-- Title -->
        <text
            x={margin.left + innerWidth / 2}
            y={margin.top / 2}
            text-anchor="middle"
            class="chart-title">
            Lines of Code by Language
        </text>

        <g transform="translate({margin.left}, {margin.top + innerHeight})"
        bind:this={xAxis} />
        <g transform="translate({margin.left}, {margin.top})"
        bind:this={yAxis} />

        <g transform="translate({margin.left}, {margin.top})">
            {#each data as d}
                <rect
                    x={0}
                    y={yScale(d.label)}
                    width={xScale(d.value)}
                    height={yScale.bandwidth()}
                    fill={colorScale(d.label)}
                />
            {/each}

            {#if maxBar}
                <!-- highlight outline -->
                <rect
                    x={0}
                    y={yScale(maxBar.label)}
                    width={xScale(maxBar.value)}
                    height={yScale.bandwidth()}
                    fill="none"
                    stroke="currentColor"
                    stroke-width="2"
                />
                <!-- leader line -->
                <line
                    x1={xScale(maxBar.value)}
                    y1={yScale(maxBar.label) + yScale.bandwidth() / 2}
                    x2={xScale(maxBar.value) + 10}
                    y2={yScale(maxBar.label) + yScale.bandwidth() / 2}
                    stroke="currentColor"
                    stroke-width="1"
                />
                <!-- annotation -->
                <text
                    x={xScale(maxBar.value) + 15}
                    y={yScale(maxBar.label) + yScale.bandwidth() / 2}
                    dominant-baseline="middle"
                    class="annotation">
                    Most lines
                </text>
            {/if}

            <!-- x-axis label -->
            <text
                x={innerWidth / 2}
                y={innerHeight + margin.bottom - 10}
                text-anchor="middle"
                class="axis-label">
                Lines of Code
            </text>

            <!-- y-axis label -->
            <text
                x={-(innerHeight / 2)}
                y={-margin.left + 20}
                text-anchor="middle"
                transform="rotate(-90)"
                class="axis-label">
                Language
            </text>
        </g>
    </svg>

    <ul class="legend">
        {#each data as d}
            <li style="--color: {colorScale(d.label)}">
                <span class="swatch"></span>
                {d.label} <em>({d.value})</em>
            </li>
        {/each}
    </ul>
</div>

<style>
svg {
    max-width: 100%;
    height: auto;
    overflow: visible;
}
.container {
    display: flex;
    align-items: center;
    gap: 2rem;
}
.legend {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 0.75rem;
    list-style: none;
    padding: 0;
    margin: 0;
}
li {
    display: flex;
    align-items: center;
    gap: 0.3rem;
}
.swatch {
    display: inline-block;
    width: 12px;
    height: 12px;
    background-color: var(--color);
    border-radius: 2px;
    flex-shrink: 0;
}

.chart-title {
    font-size: 1em;
    font-weight: bold;
    fill: currentColor;  /* inherits from the page theme */
}
.axis-label {
    font-size: 0.6em;
    fill: currentColor;
    opacity: 0.6;        /* slightly muted without hardcoding gray */
}
.annotation {
    font-size: 0.7em;
    fill: currentColor;
    font-style: italic;
}
:global(.domain) { stroke: currentColor; opacity: 0.5; }
:global(.tick line) { stroke: currentColor; opacity: 0.5; }
:global(.tick text) { fill: currentColor; font-size: 0.75em; }

</style>