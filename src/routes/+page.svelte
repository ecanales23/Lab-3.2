<script>
  import projects from "$lib/projects.json";
  import Project from "$lib/Project.svelte";
  import Reading from "$lib/ReadingItem.svelte";
  import readings from "$lib/reading.json";
</script>
<h1>Emily Canales</h1> 

<p>Hi there! My name is Emily Canales. I am a senior at MIT in Course 11 (Urban Planning). </p>
<img src = "images/linkedin photo.jpg" alt = "Picture of myself">

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

