<script>
import Scrolly from "svelte-scrolly";
import projects from "$lib/projects.json";
let scrollyProgress = 0

let sorted_projects = projects.sort((a, b) => a.year - b.year)

let progressPerProject = 100 / projects.length;

$: activeProjectIdx = Math.min(
  projects.length - 1,
  Math.floor(scrollyProgress / progressPerProject)
);

</script>

<div class="scrolly-wrapper">
  <Scrolly bind:progress={scrollyProgress}>
    
    <!-- Narrative (scrolls on the left) -->
    {#each projects as project}
      <section class="step">
        <div class="step-content">
          <h3>{project.title}</h3>
          <p>{project.story}</p>
        </div>
      </section>
    {/each}

    <!-- Visualization (stays sticky on the right) -->
    <svelte:fragment slot="viz">
      <div class="project-detail">
        <h3>{projects[activeProjectIdx].year}</h3>
        <img 
          src={projects[activeProjectIdx].image} 
          alt={projects[activeProjectIdx].title} 
        />
      </div>
    </svelte:fragment>

  </Scrolly>
</div>


<style>
.scrolly-wrapper {
  width: min(1000ch, 60vw);
  position: relative;
  left: 50%;
  transform: translateX(-50%);
}

.step {
  min-height: 80vh;
  padding: 2rem;
}

.step-content {
  border-left: 2px solid var(--accent-color);
  padding: 1.5rem 2rem;
}

.project-detail {
  padding: 2rem;
  width: 100%;
}

.project-detail img {
  width: 100%;
}
</style>