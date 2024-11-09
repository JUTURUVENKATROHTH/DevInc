<script>
	import { onMount } from 'svelte';

	//variables
	
	let amount = 0;
	let fromCurrency = 'usd';
	let toCurrency = 'eur';
	let conversionRate = null;
	let currencies = [];
	
	// const API_KEY = 'YOUR_API_KEY';
	const API_URL = 'https://latest.currency-api.pages.dev/v1/currencies/usd.json';
	
	onMount(async () => {
		try {
			const response = await fetch(API_URL);
			const data = await response.json();
			currencies = Object.keys(data.usd);
			conversionRate = data.usd[toCurrency];
			
			
		}
		catch(error){
			console.error("Error fetching currencies:", error);
		}
	});

	async function fetchConversionRate(){
		if(fromCurrency == 'usd'){
			const response = await fetch(API_URL);
			const data = await response.json();
			conversionRate = data.usd[toCurrency];
			
		} else {
			const response = await fetch(API_URL);
			const data = await response.json();
			const usdtofromCurrency = data.usd[fromCurrency];
			const usdtotoCurrency = data.usd[toCurrency];
			conversionRate = usdtotoCurrency/usdtofromCurrency;
			
		}
	}

	$: convertedAmount= conversionRate ? (amount * conversionRate).toFixed(2): '...';


	
</script>

<div class = "converter">
	<h1> Currency converter</h1>
	
		<div class = "table">
			<input bind:value={amount} type = "number" id = "input_currency"/>
			<!-- <p>from</p> -->
			<select bind:value={fromCurrency}  class = 'currency'>
				{#each currencies as currency}
            <option value={currency}>{currency}</option>
        {/each}<option>Select</option>
			</select>
			<p>to</p>
			<input bind:value={convertedAmount} type = "number" id = "output_currency"/>
			<select bind:value={toCurrency} class = 'currency'>
				{#each currencies as currency}
            <option value={currency}>{currency}</option>
        {/each}
			</select>
		</div>
	
	<button onclick={fetchConversionRate}>Convert</button>
	<p>Converted amount : {convertedAmount} {toCurrency}</p>
</div>

