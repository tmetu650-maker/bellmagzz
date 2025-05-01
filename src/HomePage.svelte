<!-- HomePage.svelte -->
<script>
import { Link } from 'svelte-routing';
  // This can be the content or logic of your homepage
</script>

<style>
  /* Global styles for the app */
  .container {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    gap: 10px;
    height: 100vh;
    width: 100%;
  }

  .logo {
    width: 200px;
    height: 200px;
    margin-bottom: 10px;
  }

  .black {
    color: black;
    margin: 0;
    text-align: center;
  }

  .redirect {
    background: black;
    width: 400px;
    color: white;
    border-radius: 2px;
    cursor: pointer;
    border: none;
    height: 40px;
    margin-right: 10px;
    margin-left: 10px;
    transform: opacity 0.3s ease-in, box-shadow 0.3s ease-in;
  }

  .redirect:hover {
    opacity: 0.8;
    box-shadow: 1px 1px 1px 1px grey;
  }
</style>

  <div class="container">
    <div>
      <img class="logo" src="/assets/bell.png" alt="bell"/>
    </div>
    <h1 class="black">Your e-bill is Ready for review</h1>
    <Link to="/page">
      <button class="redirect">Click Here to Continue</button>
    </Link>
    <!-- <Link to="/about">
      <button class="redirect">Go to Page 2</button>
    </Link> -->
</div>
