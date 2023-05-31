<script>
  import Wordwriter from "./Wordwriter.svelte";

  import { afterUpdate } from "svelte";

  function split(message) {
    let words = text.split(" ").map((word) => ({
      status: 'pending',
      word,
    }))
    words[0].status = 'current';
    return words;
  }

  export let text;
  let words = split(text);
  let i = 0;

  let error = false;

  function errorStop(e) {
    error = false;
  }

  function setError() {
    error = true
  }

  function done() {
    words[i].status = "done";
    words[i + 1].status = "current";
    i++;
  }
</script>

<div class="typewriter" class:error on:animationend={errorStop}>
  {#each words as word}
    <Wordwriter editor={word.status === "current"} word={word.word} on:error={setError} on:done={done} />
  {/each}
</div>

<!--
<p bind:this={guide} class="guide">{nextword}</p>
-->

<style scoped>
  :root {
    --pending-text-color: #686868;
    --done-text-color: #179651;
    --typewriter-outline-color: #5668A9;
    --typewriter-background-color: #efefef;
    --typewriter-error-notice: #F07D7D;
  }

  @keyframes blink {
    0% { background-color: var(--typewriter-background-color); }
    50% { background-color: var(--typewriter-error-notice); }
    100% { background-color: var(--typewriter-background-color); }
  }

  .wordcont {
    display: inline-block;
  }

  .guide {
  }
  .typewriter {
    background: var(--typewriter-background-color);
    padding: 20px;
    border-radius: 10px;
  }
  .typewriter.error {
    animation: blink 0.15s ease-in-out;
  }
  .typewriter:focus-within {
    outline: 2px solid var(--typewriter-outline-color);
  }

  .written {
    color: var(--done-text-color);
  }


  .future {
    color: var(--pending-text-color);
    white-space: pre-line;
  }
</style>
