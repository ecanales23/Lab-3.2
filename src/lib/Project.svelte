<script>
    export let data = {};
</script>


<article>
    <h2 class="title">{data.title}
    </h2>
    <img class="projects" src={data.image} alt={data.title}>
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
</style>
