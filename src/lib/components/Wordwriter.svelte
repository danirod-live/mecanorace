<script>
  import { afterUpdate, onMount, createEventDispatcher } from "svelte";
  export let word;

  export let editor = false;

  const emit = createEventDispatcher();

  let pending = word;
  let written = "";
  let width = 0;

  let status;
  $: {
    if (editor) {
      status = "editing";
    } else if (written === "") {
      status = "pending";
    } else {
      status = "done";
    }
  }

  let textarea, reference;

  $: {
    if (textarea && editor) {
      textarea.focus();
    }
  }

  afterUpdate(() => {
    if (reference) {
    width = reference.clientWidth;
    }
  });

  function input(e) {
    // mikelego3ds -- he mirado en chatgpt puedes detectar la tecla acento y despues detectas la siguiente tecla con evento con {once:true} en el addEventListener 
    if (['´', '`', '¨', '^'].includes(e.data)) {
      return;
    }
    const letter = e.data;
    if (letter === pending.charAt(0)) {
      written += letter;
      pending = pending.slice(1);
    } else if (letter === " " && pending === "") {
      emit('done');
    } else {
      emit('error');
    }
    e.target.value = "";
  }
</script>

<span class={['word', status].join(' ')}>
  {#if written}
    <span class="written">{written}</span>
  {/if}
  {#if editor}
    <input style:--width={`${width}px`} bind:this={textarea} on:input={input} type="text" placeholder={pending} class="input">
    <span class="reference" bind:this={reference}>{pending}</span>
  {:else}
    {pending}
  {/if}
</span>

  <style scoped>
  .word.pending { --text-color: silver; }
  .word.editing { --text-color: black; }
  .word.done { --text-color: green; }

  .word {
    margin-right: 0.4ch;
    display: inline-flex;
  }

  .input {
    font-family: inherit;
    font-size: inherit;
    color: currentColor;
    border: none;
    padding: 0;
    outline: none;
    background: none;
    width: var(--width);
  }

  .input::placeholder {
    color: var(--pending-text-color);
    opacity: 1;
  }

  .reference {
    visibility: hidden;
    display: inline;
    position: absolute;
    white-space: pre;
  }

  .reference, .written {
    color: var(--text-color);
  }
  </style>
