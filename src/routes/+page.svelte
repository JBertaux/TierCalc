<script lang="ts">
	import TierResult from "./TierResult.svelte";

    let tierCredits = $state(""); 
    let isValid = $derived(tierCredits !== null && tierCredits !== "" && !isNaN(Number(tierCredits)));
  
    const onsubmit = (event: { preventDefault: () => void; }) => {
      event.preventDefault(); // Prevent form from reloading the page
    };
</script>

<style>
    .form-container {
      max-width: 400px;
      margin: 2rem auto;
      padding: 1rem;
      border: 1px solid #ccc;
      border-radius: 8px;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
      background-color: #f9f9f9;
    }
  
    .form-container h1 {
      margin-bottom: 1rem;
      font-size: 1.5rem;
      text-align: center;
    }
  
    .form-container form {
      display: flex;
      flex-direction: column;
      gap: 1rem;
    }
  
    .form-container input {
      padding: 0.5rem;
      font-size: 1rem;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
  
    .form-container button {
      padding: 0.5rem;
      font-size: 1rem;
      border: none;
      border-radius: 4px;
      background-color: #0070ff;
      color: #fff;
      cursor: pointer;
    }
  
    .form-container button:hover {
      background-color: #005bb5;
    }
</style>

<div class="form-container">
    <h1>TierCalc</h1>
    <form {onsubmit}>
      <label for="tierCredits">Enter your tier credits:</label>
      <input
        bind:value={tierCredits}
        type="number"
        step="0.5"
        id="tierCredits"
        placeholder="e.g. 1200"
        required
      />
      <button type="submit">Compute</button>
    </form>
</div>

{#if isValid === true}
    {#key tierCredits}
        <TierResult {tierCredits}/>
    {/key}
{/if}

