<script>
  export let index = 0
  export let count = 0
  export let offset = 0
  export let progress = 0
</script>

<div class="debug_scroller">

  <p class="p_debug">
    current section (index + 1): <strong>{index + 1} / {count}</strong> (count)
  </p>
  <progress value={count ? (index + 1) / count : 0}></progress>
  
  <p class="p_debug">(offset) in current section <b>{offset}</b></p>
  <progress value={offset || 0}></progress>
  
  <p class="p_debug">total (progress) <b>{progress}</b></p>
  <progress value={progress || 0}></progress>
</div>

<style>
  .debug_scroller {
    position: fixed;
    bottom: 10px;
    left: 10px;
  }
  .p_debug {
    font-size: 0.8em;
    margin: 0;
    padding: 0;
    color: aliceblue;
  }
</style>
