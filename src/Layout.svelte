<script>
  export let prefix = "/docs";
  export let brand = "sveature";
  export let component, feature, components;

  function slugify(str = "") {
    return str.replace(/ /g, "--");
  }

  function deslugify(str = "") {
    return str.replace(/--/g, " ");
  }

  let featureId;
  $: featureId = deslugify(feature);

  let toggled = false;
</script>

<style>
  :global(html, body) {
    margin: 0;
    padding: 0;
    font-family: "Helvetica Neue", Helvetica, sans-serif;
    font-size: 16px;
    height: 100vh;
    width: 100vw;
    overflow: hidden;
  }
  header {
    height: 64px;
    line-height: 64px;
    background: var(--sveature-header-bg);
    color: var(--sveature-header-fg);
    display: flex;
  }
  header .brand {
    width: 300px;
    text-align: center;
    background: var(--sveature-brand-bg);
  }
  header .brand > a {
    text-decoration: none;
    color: var(--sveature-brand-fg);
  }
  header .brand > a > h1 {
    margin: 0;
    padding: 0;
  }
  header .title {
    flex: 1 1 auto;
  }
  header .title h2 {
    margin: 0;
    padding: 0 16px;
  }
  main {
    display: flex;
    height: calc(100vh - 64px);
    width: 100vw;
  }
  main :global(.theme) {
    background: transparent;
  }
  main :global(.theme h1) {
    margin: 0;
  }
  main :global(hr) {
    margin-top: 16px;
    border: none;
    border-top: 2px solid hsl(260, 10%, 90%);
  }
  .sidebar {
    width: 300px;
    height: calc(100vh - 64px);
    flex-grow: 0;
    flex-shrink: 0;
    background: #eee;
    overflow: auto;
  }
  .sidebar ul {
    list-style: none;
    font-size: 21px;
  }
  .sidebar > ul {
    margin: 0;
    padding: 0;
  }
  .sidebar > ul > li a {
    display: block;
    padding: 8px;
  }
  .sidebar > ul > li > ul {
    margin: 0;
    padding: 0 0 0 16px;
    border-bottom: 1px solid hsl(0, 0%, 85%);
    background: hsl(0, 0%, 98%);
  }
  .sidebar > ul > li > ul > li > a {
    display: block;
    background: hsl(0, 0, 0, 92%);
  }
  .sidebar > ul > li > ul > li > a:hover {
    background: hsl(0, 0, 0, 87%);
  }
  .sidebar > ul > li > a {
    font-size: 18px;
    font-weight: bold;
    color: #111;
    background: hsl(0, 0%, 90%);
  }
  .sidebar > ul > li > a:hover {
    background: hsl(0, 0%, 85%);
  }
  .sidebar a {
    text-decoration: none;
    color: var(--sveature-link-color);
  }
  .content {
    padding: 8px 16px;
    width: 100%;
    overflow: auto;
  }
  h2 {
    font-size: 42px;
    margin: 0;
  }
  .sidebar-toggle {
    display: none;
    width: 64px;
  }
  @media (max-width: 420px) {
    header .brand:not(.toggled) {
      display: none;
    }
    header .title {
      width: calc(100% - 120px);
    }
    header .title h2 {
      font-size: 24px;
    }
    header .title.toggled {
      display: none;
    }
    .sidebar:not(.toggled) {
      display: none;
    }
    .sidebar-toggle:not(.toggled) {
      background: hsl(260, 70%, 25%);
    }
    .sidebar.toggled {
      width: 100%;
    }
    .sidebar-toggle {
      display: block;
      text-align: center;
      flex: 1;
    }
  }
  :root {
    --sveature-brand-bg: hsl(260, 70%, 40%);
    --sveature-brand-fg: hsl(260, 70%, 98%);
    --sveature-header-bg: hsl(260, 70%, 30%);
    --sveature-header-fg: hsl(260, 70%, 97%);
    --sveature-link-color: hsl(260, 70%, 30%);
    --sveature-section-bg: hsl(0, 0%, 96%);
    --sveature-section-fg: hsl(0, 0%, 18%);
  }
</style>

<header>
  <div class="brand" class:toggled>
    <a href={`${prefix}/`}><h1>{brand}</h1></a>
  </div>
  <div class="title" class:toggled>
    {#if component}
      <h2>
        {component}
        {#if feature}/ {deslugify(feature)}{/if}
      </h2>
    {:else}
      <h2>Home</h2>
    {/if}
  </div>
  <div
    class="sidebar-toggle"
    class:toggled
    on:click={() => {
      toggled = !toggled;
    }}>
    {#if toggled}❌{:else}🔗{/if}
  </div>
</header>

<slot name="top" />

<main>
  <div class="sidebar" class:toggled>
    <slot name="sidebar">
      <ul>
        {#each Object.entries(components) as [componentName, cdef]}
          <li>
            <a href={`${prefix}/${componentName}`}>{componentName}</a>
            <ul>
              {#each Object.keys(cdef) as featureName}
                <li>
                  <a
                    href={`${prefix}/${componentName}/${slugify(featureName)}`}>{featureName}</a>
                </li>
              {/each}
            </ul>
          </li>
        {/each}
      </ul>
    </slot>
  </div>
  <div class="content">
    <slot name="content">
      {#if component && feature && components[component] && components[component][featureId]}
        <svelte:component this={components[component][featureId]} />
      {:else if component && !feature && components[component]}
        {#each Object.entries(components[component]) as [featureName, Component]}
          <h2>{featureName}</h2>
          <div class="feature">
            <svelte:component this={Component} />
          </div>
        {/each}
      {:else}
        <slot>
          <h1>404</h1>
          <p>The thing you're looking for could not be found.</p>
        </slot>
      {/if}
    </slot>
  </div>
</main>
