<script>
  import { onMount } from "svelte";
  import { fade } from "svelte/transition";
  import randomColor from "randomcolor";

  const getQuote = async () => {
    try {
      const res = await fetch("https://api.quotable.io/random");
      if (res.ok) {
        return res.json();
      }
      return new Error("Error encountered!");
    } catch (err) {
      return err;
    }
  };

  let currentQuote;
  let color = "grey";

  const getNewQuote = async () => {
    try {
      currentQuote = await getQuote();
      color = randomColor({ luminosity: "dark" });
    } catch (err) {
      console.log(err.message);
    }
  };

  onMount(() => {
    getNewQuote();
  });
  $: document.documentElement.style.setProperty("--color", color);
</script>

<header>
  <nav class="navbar bg-light">
    <div class="container-fluid">
      <span class="navbar-brand fw-bold">
        <img
          src="src/assets/favicon.svg"
          alt="Logo"
          width="30"
          height="24"
          class="d-inline-block align-text-top"
        />
        Random Quote Machine
      </span>
    </div>
  </nav>
</header>

<main>
  {#if currentQuote}
    <div
      transition:fade
      class="container bg-white p-5 mt-5 rounded-2"
      id="quote-box"
    >
      {#key currentQuote}
        <section in:fade>
          <figure>
            <blockquote class="text-center">
              <p style:color id="text" class="fs-3">
                <i class="bi bi-quote" />
                {currentQuote.content}
              </p>
            </blockquote>
            <figcaption
              style:color
              class="fs-5 text-end fst-italic"
              id="author"
            >
              - {currentQuote.author}
            </figcaption>
          </figure>
        </section>
      {/key}

      {#key color}
        <section>
          <div in:fade class="d-inline pt-3">
            <a
              style:background-color={color}
              class="btn text-white"
              id="tweet-quote"
              href={`https://twitter.com/intent/tweet?hashtags=quotes&text="${currentQuote.content}" ${currentQuote.author}`}
              target="_blank"><i class="bi bi-twitter" /></a
            >
            <a
              style:background-color={color}
              class="btn text-white float-end"
              id="new-quote"
              on:click={getNewQuote}>New quote</a
            >
          </div>
        </section>
      {/key}
    </div>
  {/if}
</main>

<footer class="text-white text-center mt-2">
  By <a
    class="text-white text-decoration-none fw-bold"
    href="https://github.com/harrmitch">harrmitch</a
  >
</footer>

<style>
  :global(body) {
    background-color: var(--color);
  }
</style>
