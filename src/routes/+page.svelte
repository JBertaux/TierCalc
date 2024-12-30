<script lang="ts">
	import TierResult from "./TierResult.svelte";

    let tierCredits = $state(""); 
    let isValid = $derived(tierCredits !== null && tierCredits !== "" && !isNaN(Number(tierCredits)));
  
    const onsubmit = (event: { preventDefault: () => void; }) => {
      event.preventDefault(); // Prevent form from reloading the page
    };
</script>

<div class="max-w-md mx-auto mt-8 p-6 border border-gray-300 rounded-lg shadow-lg bg-gray-50">
    <h1 class="text-2xl font-bold mb-4 text-center">TierCalc</h1>
    <form {onsubmit} class="flex flex-col gap-4">
      <label for="tierCredits" class="text-lg">Enter your tier credits:</label>
      <input
        bind:value={tierCredits}
        type="number"
        step="0.5"
        id="tierCredits"
        placeholder="e.g. 1200"
        required
        class="p-2 border border-gray-300 rounded"
      />
      <button type="submit" class="p-2 bg-blue-500 text-white rounded hover:bg-blue-700">Compute</button>
    </form>
</div>

{#if isValid === true}
    {#key tierCredits}
        <TierResult {tierCredits}/>
    {/key}
{/if}

