<script>
import { base } from "$app/paths";
import { page } from "$app/stores";

let localStorage = globalThis.localStorage ?? {};
let colorScheme = "light dark";
 if (localStorage.colorScheme) {
    colorScheme = localStorage.colorScheme;
  }

let root = globalThis.document?.documentElement;

$: root?.style.setProperty("color-scheme", colorScheme);
$: localStorage.colorScheme = colorScheme;

let pages = [
  {url: "/", title: "About"},
  {url: "/projects", title: "Projects"},
  {url: "/resume", title: "Resume"},
  {url: "/contact", title: "Contact"},
  {url: "https://github.com/ecanales23", title: "Github"},
  // add other pages here
];

</script>

<label class="color-scheme-switch">
  Theme:
    <select bind:value={colorScheme}>
    <option value="light dark">Automatic</option>
    <option value="light">Light</option>
    <option value="dark">Dark</option>
  </select>
</label>


<!-- Navigation bar -->
<nav> 
  {#each pages as p}
    <a
      href={p.url.startsWith("http") ? p.url : base + p.url}
      class:current={
        p.url === "/"
          ? $page.url.pathname === base + "/"
          : $page.url.pathname.startsWith(base + p.url)
      }
      target={p.url.startsWith("http") ? "_blank" : null}
    >
      {p.title}
    </a>
  {/each}
</nav>

<slot />
<!-- Styling Navigation Bar -->

<style>
nav ul,
nav li {
  display: contents;
}

nav {
  display: flex;
  margin-block: 1.5rem;
}

nav a {
  flex: 1;
  text-align: center;
  text-decoration: none;
  padding: 0.5rem;
  color: inherit;
}

nav a.current {
  border-bottom: 0.4em solid var(--lighter-gray);
}

nav a:hover {
  border-bottom: 0.4em solid var(--color-accent);
}

/* Light/Dark Mode */

  :root {
    color-scheme: light dark;
  }

  nav {
    --border-color: oklch(50% 10% 200 / 40%);
    border-bottom: 2px solid var(--border-color);
  }

  nav a.current {
    border-bottom: 4px solid var(--border-color);
  }

  /* Optional hover fix */
  nav a:hover {
    background-color: color-mix(in oklch, var(--color-accent), canvas 85%);
  }

.color-scheme-switch {
  position: absolute;
  top: 1rem;
  right: 1rem;
  display: inline-flex;
  gap: 4px;
  font-size: 90%;
}

select {
  font-family: inherit;
}

</style>

