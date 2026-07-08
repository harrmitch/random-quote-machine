<script>
  import { fade } from "svelte/transition";
  import randomColor from "randomcolor";

  let currentQuote = $state(null);
  let color = $state("grey");

  const getQuote = async () => {
    try {
      const res = await fetch("https://api.quotable.io/quotes/random");
      if (res.ok) {
        const data = await res.json();
        return data[0];
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
      console.log(err);
    }
  };

  $effect(() => {
    getNewQuote();
  });

  $effect(() => {
    document.documentElement.style.setProperty("--color", color);
  });
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
              <i class="bi bi-quote"></i>
              {currentQuote.content}
            </p>
          </blockquote>
          <figcaption style:color class="fs-5 text-end fst-italic" id="author">
            by {currentQuote.author}
          </figcaption>
        </figure>
      </section>
    {/key}

    {#key color}
      <section>
        <div in:fade class="d-inline pt-3">
          <a aria-label="Tweet this quote!"
            style:background-color={color}
            class="btn text-white"
            id="tweet-quote"
            href={`https://twitter.com/intent/tweet?hashtags=quotes&text="${currentQuote.content}" ${currentQuote.author}`}
            target="_blank"><i class="bi bi-twitter"></i></a
          >
          <button
            style:background-color={color}
            class="btn text-white float-end"
            id="new-quote"
            onclick={getNewQuote}>New quote</button
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
