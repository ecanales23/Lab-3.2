<svelte:head>
  <title>Meta</title>
</svelte:head>

<h1>Meta</h1>

<dl class="stats">
    <dt>Total <abbr title="Lines of code">LOC</abbr></dt>
    <dd>{locData.length}</dd>
    <dt>Total Commits</dt>
    <dd>{commits.length}</dd>
    <dt>Most Active Time</dt>
    <dd>{mostActiveTime}</dd>
</dl>

<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import BarHorizontal from '$lib/BarHorizontal.svelte';
    import {
        computePosition,
        autoPlacement,
        offset,
    } from '@floating-ui/dom';
    import LineChart from '$lib/LineChart.svelte';

    let locData = [];
    let locBarData = [];
    let commits = [];
    let mostActiveTime = "";
    let width = 1000, height = 600;
    let margin = { top: 10, right: 10, bottom: 30, left: 20 };
    let usableArea = {
        top: margin.top,
        right: width - margin.right,
        bottom: height - margin.bottom,
        left: margin.left
    };
    usableArea.width = usableArea.right - usableArea.left;
    usableArea.height = usableArea.bottom - usableArea.top;
    let xAxis, yAxis, yAxisGridlines;
    let hoveredIndex = -1;
    let cursor = {x: 0, y: 0};
    let commitTooltip;
    let tooltipPosition = {x: 0, y: 0};
    let clickedCommits = [];
    let svg;
    let brushSelection = null;
    let linesByDate = [];


   $: {
    d3.select(svg).call(d3.brush()
        .extent([[usableArea.left, usableArea.top], [usableArea.right, usableArea.bottom]])
        .on("start brush end", brushed)
    );
    d3.select(svg).selectAll(".dots, .overlay ~ *").raise();
    }

    $: hoveredCommit = commits[hoveredIndex] ?? hoveredCommit ?? {};

    $: brushedCommits = brushSelection ? commits.filter(isCommitBrushed) : [];
    $: selectedCommits = Array.from(new Set([...clickedCommits, ...brushedCommits]));
    $: rScale = d3.scaleSqrt()
                .domain(d3.extent(commits, d => d.totalLines))
                .range([5, 30]);
    $: [minDate, maxDate] = d3.extent(commits.map(d => d.date));
    $: maxDatePlusOne = new Date(maxDate);
    $: maxDatePlusOne.setDate(maxDatePlusOne.getDate() + 1);

    $: xScale = d3.scaleTime()
                .domain([minDate, maxDatePlusOne])
                .range([usableArea.left, usableArea.right])
                .nice();

    $: yScale = d3.scaleLinear()
                .domain([24, 0])
                .range([usableArea.bottom, usableArea.top]);
    $: {
        d3.select(xAxis).call(d3.axisBottom(xScale));
        d3.select(yAxis).call(
            d3.axisLeft(yScale)
            .tickFormat(d => String(d % 24).padStart(2, "0") + ":00")
        );
        d3.select(yAxisGridlines).call(
            d3.axisLeft(yScale)
            .tickFormat("")
            .tickSize(-usableArea.width)
        );
    }
    $: selectedLines = (selectedCommits.length > 0 ? selectedCommits : commits).flatMap(d => d.lines);

    $: selectedCounts = d3.rollup(selectedLines, v => v.length, d => d.type);

    $: allTypes = Array.from(new Set(locData.map(d => d.type)));

    $: barData = allTypes.map(type => ({ 
        label: String(type), 
        value: selectedCounts.get(type) || 0 
    }));

    $: {
	// 1. Get the count for each date in the data
	const rolled = d3.rollups(
		locData,
		v => v.length,
		d => d3.timeDay.floor(d.datetime)
	).map(([date, count]) => ({ date, count }));

	// 2. Get an array of all days covered by the data
	const [minDate, maxDate] = d3.extent(rolled, d => d.date);
	const allDays = d3.timeDays(minDate, d3.timeDay.offset(maxDate, 1));

	// 3. Build linesByDate by filling all undefined dates with 0 counts
	linesByDate = allDays.map(date => ({
		date,
		count: rolled.find(d => d.date.getTime() === date.getTime())?.count ?? 0
	}));

}

    onMount(async () => {
    
    locData = await d3.csv(`${base}/loc.csv`, row => ({
        ...row,
        line: Number(row.line),
        depth: Number(row.depth),
        length: Number(row.length),
        date: new Date(row.date + "T00:00" + row.timezone),
        datetime: new Date(row.datetime)
    }));

    commits = d3.groups(locData, d => d.commit).map(([commit, lines]) => {
        let first = lines[0];
        let {author, date, time, timezone, datetime} = first;
        return {
            id: commit,
            url: "https://github.com/ecanales23/Lab-3.2/commit/" + commit,
            author, date, time, timezone, datetime,
            hourFrac: datetime.getHours() + datetime.getMinutes() / 60,
            totalLines: lines.length,
            lines: lines
        };

    }); // <-- .map() closes here
    commits = d3.sort(commits, d => -d.totalLines);
    // now hourGroups can see the completed commits array
    const hourGroups = d3.rollups(commits, v => v.length, d => {
        const h = d.datetime.getHours();
        if (h >= 5 && h < 12) return "Morning";
        if (h >= 12 && h < 17) return "Afternoon";
        if (h >= 17 && h < 21) return "Evening";
        return "Night";
    });
    mostActiveTime = d3.greatest(hourGroups, d => d[1])?.[0];

    locBarData = d3.rollups(locData, v => v.length, d => d.type)
        .sort((a, b) => b[1] - a[1])
        .map(([lang, count]) => ({ label: lang, value: count }));
    
});

async function dotInteraction(index, evt) {
    let hoveredDot = evt.target;
    if (evt.type === "mouseenter" || evt.type === "focus") {
        hoveredIndex = index;
        cursor = {x: evt.x, y: evt.y};
        if (commitTooltip) {
            tooltipPosition = await computePosition(hoveredDot, commitTooltip, {
                strategy: "fixed",
                middleware: [
                    offset(5),
                    autoPlacement()
                ],
            });
        }
    }
    else if (evt.type === "click" || (evt.type === "keyup" && evt.key === "Enter")) {
        let commit = commits[index];
        if (!clickedCommits.includes(commit)) {
            clickedCommits = [...clickedCommits, commit];
        } else {
            clickedCommits = clickedCommits.filter(c => c !== commit);
        }
    }
}
function brushed(evt) {
    brushSelection = evt.selection;
}

function isCommitBrushed(commit) {
    if (!brushSelection) return false;
    let min = {x: brushSelection[0][0], y: brushSelection[0][1]};
    let max = {x: brushSelection[1][0], y: brushSelection[1][1]};
    let x = xScale(commit.date);
    let y = yScale(commit.hourFrac);
    return x >= min.x && x <= max.x && y >= min.y && y <= max.y;
    }

    
</script>



<p>Meta page to visualize project data</p>

<BarHorizontal 
    data={barData} 
    title={selectedCommits.length > 0 ? `Lines of Code: ${selectedCommits.length} Selected Commits` : "Lines of Code by Language"}
/>
<h3>Commits by time of day</h3>

<svg viewBox="0 0 {width} {height}" bind:this={svg}>
    <g class="gridlines" transform="translate({usableArea.left}, 0)" bind:this={yAxisGridlines} />
    <g transform="translate(0, {usableArea.bottom})" bind:this={xAxis} />
    <g transform="translate({usableArea.left}, 0)" bind:this={yAxis} />
    <g class="dots">
        {#each commits as commit, index}  
            <circle
                class:selected={selectedCommits.includes(commit)} 
                cx={xScale(commit.datetime)}
                cy={yScale(commit.hourFrac)}
                r={rScale(commit.totalLines)}
                fill="steelblue"
                fill-opacity="0.5"
                tabindex="0"
                role="button"
                aria-describedby="commit-tooltip"
                aria-haspopup="true"
                on:mouseenter={evt => dotInteraction(index, evt)}
                on:mouseleave={evt => dotInteraction(index, evt)}
                on:click={evt => dotInteraction(index, evt)}
                on:keyup={evt => dotInteraction(index, evt)}
                on:focus={evt => dotInteraction(index, evt)}
                on:blur={evt => dotInteraction(index, evt)}
            />
        {/each}
    </g>
</svg>

<dl 
    class="info tooltip" 
    id="commit-tooltip"
    role="tooltip"
    hidden={hoveredIndex === -1}
    bind:this={commitTooltip}
    style="top: {tooltipPosition.y}px; left: {tooltipPosition.x}px"
>
    <dt>Commit</dt>
    <dd><a href={hoveredCommit.url} target="_blank">{hoveredCommit.id}</a></dd>
    <dt>Date</dt>
    <dd>{hoveredCommit.datetime?.toLocaleString("en", {dateStyle: "full"})}</dd>
    <dt>Time</dt>
    <dd>{hoveredCommit.time}</dd>
    <dt>Author</dt>
    <dd>{hoveredCommit.author}</dd>
    <dt>Lines edited</dt>
    <dd>{hoveredCommit.totalLines}</dd>
</dl>

<LineChart data={linesByDate} />

<style>
dl.stats {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 0.5em 1em;
}

dl.stats dt {
    font-weight: bold;
    color: gray;
    grid-column: 1;
}

dl.stats dd {
    margin: 0;
    grid-column: 2;
}

svg {
    overflow: visible;
}
.gridlines {
    stroke-opacity: .2;
}
circle {
    transition: 200ms;
    &:hover {
        fill: rgb(81, 33, 255);
    }
}

dl.info {
    display: grid;
    grid-template-columns: auto 1fr;
    gap: 0.25em 1em;
    margin: 0;
    transition-duration: 500ms;
    transition-property: opacity, visibility;

    &[hidden]:not(:hover, :focus-within) {
        opacity: 0;
        visibility: hidden;
    }
}

.tooltip {
    position: fixed;
    background: var(--background, canvas);
    color: var(--text, canvastext);
    padding: 0.75em 1em;
    border-radius: 6px;
    box-shadow: 0 4px 16px oklch(0% 0% 0 / 20%);
    backdrop-filter: blur(4px);
}
.selected {
    fill: var(--color-accent);
}

@keyframes marching-ants {
	to {
		stroke-dashoffset: -8; /* 5 + 3 */
	}
}

svg :global(.selection) {
	fill-opacity: 10%;
	stroke: currentColor;
	stroke-opacity: 70%;
	stroke-dasharray: 5 3;
	animation: marching-ants 2s linear infinite;
}

</style>