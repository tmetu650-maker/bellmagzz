<script>
</script>

<style>
  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    height: 100vh;
  }

  .page-header {
    color: black;
    text-align: center;
  }

  .button {
    background-color: black;
    color: white;
    padding: 10px;
    border-radius: 5px;
    cursor: pointer;
    margin-top: 20px;
  }

  .button:hover {
    opacity: 0.8;
  }
</style>

<div class="container">
  <h1 class="page-header">Page 2 - Welcome!</h1>
  <p>This is the second page of your app.</p>
  <button class="button" on:click={() => window.history.back()}>Go Back</button>
</div>
