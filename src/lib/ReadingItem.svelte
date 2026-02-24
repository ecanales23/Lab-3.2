<script>
  export let data = {};
</script>

<article class="reading">
  <img class="cover" src={data.image} alt={data.title} />
  <div class="meta">
    <h3 class="title">{data.title}</h3>
    <p class="author">{data.author}</p>
  </div>
</article>

<style>
img {
  max-width: 25%;
  height: auto;
}
  .reading {
    display: flex;
    gap: 1rem;
    align-items: center; /* vertically centers text next to cover */
    padding: 0.75rem;
  }


  .meta {
    display: flex;
    flex-direction: column;
    gap: 0.25rem;
  }

  .title {
    margin: 0;
    font-weight: 700;
  }

  .author {
    margin: 0;
  }
 
  .reading-panel {
    padding: 1.5rem;
    background: #c8d0d0; 
  }

  h2 {
    margin: 0 0 1rem 0;
    font-size: 2rem;
    font-weight: 800;
  }

  .readings {
    display: flex;
    flex-direction: column;
    gap: 1rem; 
  }

</style>