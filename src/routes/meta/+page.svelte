<svelte:head>
  <title>Meta</title>
</svelte:head>

<script>
    import { base } from '$app/paths';
    import { onMount } from 'svelte';
    import * as d3 from 'd3';
    import BarHorizontal from '$lib/BarHorizontal.svelte';

    let locData = [];
    let locBarData = [];

    onMount(async () => {
        locData = await d3.csv(`${base}/loc.csv`, row => ({
            ...row,
            line: Number(row.line),
            length: Number(row.length),
            depth: Number(row.depth)
        }));

        locBarData = d3.rollups(locData, v => v.length, d => d.type)
            .sort((a, b) => b[1] - a[1])  // descending by line count
            .map(([lang, count]) => ({ label: lang, value: count }));
    });

</script>

<h1>Meta</h1>

<p>Meta page to visualize project data</p>

<BarHorizontal data={locBarData} />

