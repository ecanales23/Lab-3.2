<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import Reading from "$lib/ReadingItem.svelte";
  import readings from "$lib/reading.json";
  import { onMount } from "svelte";

  let githubData = null;
  let loading = true;
  let error = null;

  onMount(async () => {
    try {
      let response = await fetch("https://api.github.com/users/ecanales23");
      githubData = await response.json();
    } catch (err) {
      error = err;
    }
    loading = false;
  });

</script>
<h1>Emily Canales</h1> 

<p>Hi there! My name is Emily Canales. I am a senior at MIT in Course 11 (Urban Planning). </p>
<img src = "images/linkedin photo.jpg" alt = "Picture of myself">

{#if loading}
  <p>Loading...</p>
{:else if error}
  <p>Something went wrong: {error.message}</p>
{:else}
  <section>
    <h2>My GitHub Stats</h2>
    <dl>
      <dt>Followers</dt>     <dd>{githubData.followers}</dd>
      <dt>Following</dt>     <dd>{githubData.following}</dd>
      <dt>Public Repos</dt>  <dd>{githubData.public_repos}</dd>
      <dt>Public Gists</dt>  <dd>{githubData.public_gists}</dd>
    </dl>
  </section>
{/if}

<style>
  dl {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
  }

  dt {
    grid-row: 1;
  }

  dd {
    grid-row: 2;
  }
</style>

<h2>Latest Projects</h2>
<div class="projects">
    {#each projects.slice(0, 3) as p}
    <Project data={p} />
    {/each}
</div>


<section class="reading-panel">
  <h2>What I’m Reading</h2>

  <div class="readings">
    {#each readings as r}
      <Reading data={r} />
    {/each}
  </div>
</section>

