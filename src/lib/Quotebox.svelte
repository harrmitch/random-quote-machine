<script>
  import { fade } from "svelte/transition";
  import { onMount } from "svelte";
  import randomColor from "randomcolor";

  let currentQuote;
  let color = "grey";

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
          <figcaption style:color class="fs-5 text-end fst-italic" id="author">
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

<style>
  :global(body) {
    background-color: var(--color);
  }
</style>
