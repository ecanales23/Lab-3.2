<script>
    export let data = {};
</script>


<article>
  <h2 class="title">
      {#if data.url}
          <a href={data.url} target="_blank">{data.title}</a>
      {:else}
          {data.title}
      {/if}
  </h2>
    <img class="projects" src={data.image} alt={data.title}>
    <p class="year">{data.year}</p>
    <p>{data.description}</p>
</article>


<!-- Project Page Styling -->
<style>
img {
  max-width: 75%;
  height: auto;
}
article {
  display: grid;
  grid-template-rows: subgrid;
  grid-row: span 3;
  border: 1px solid var(--lighter-gray);
  border-radius: 6px;
  padding: 1em;
}
h2 {
  margin: 0;
}
.year {
  font-size: 0.85rem;
  color: gray;
  margin: 0.25em 0;
  font-style: italic;
}

a {
    color: inherit;
    text-decoration: none;
    &:hover {
        text-decoration: underline;
    }
}
</style>
