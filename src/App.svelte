<script>
	import Fa from 'svelte-fa'
	import { faCreditCard, faUsers, faHeart, faShareAlt } from '@fortawesome/free-solid-svg-icons';

	let tips = false;
	let calcNotFinished = true;
	let errored = false;

	let cost = 0;
	let people = 1;
	let tip = 0;
	let total = 0;

	const invertTip = () => (tips = !tips);
	const splitMoney = () => {
		if(cost === null || people === null || tip === null || cost?.toString()?.split('.')[1]?.length > 2 || !(Number.isInteger(people)) || people < 1 || tip > 100 || tip < 0 ) {
			errored = true;
		}
		total = ((cost + (cost * (tip / 100))) / people).toFixed(2);
		calcNotFinished = false;
	}
	const resetValues = () => {
		cost = 0;
		people = 1;
		tip = 0;
		total = 0;
		tips = false;
		if(!calcNotFinished) calcNotFinished = true;
	}
	const copyText = () => {
		navigator.clipboard.writeText(`Bill Amount: $${cost}\nNumber of People: ${people}\nTip Percent: ${tip}%\n-------------------------------\nTotal Amount: $${(cost + (cost * (tip / 100))).toFixed(2)}\nEveryone's share: $${total}`)
		alert('Copied text to clipboard.')
	}
</script>

<main>
	<div class="container">
		<h1>Eating Out Bill Splitter</h1>
		<p class="large"><Fa icon={faCreditCard} color="#2759DB" /> Total amount:</p>
		<span class="dollar">
			<input type="number" bind:value={cost} min="0" step="0.01" readonly={!calcNotFinished} style="width:300px;" />
		</span>
		<p class="large"><Fa icon={faUsers} color="#2759DB" /> Number of people:</p>
		<input type="number" bind:value={people} min="1" step="1" readonly={!calcNotFinished} style="width:300px;" />

		{#if tips}
			<p class="large tip-text"><Fa icon={faHeart} color="#2759DB" /> <span class="highlight pointer" on:click="{invertTip}">Close tip menu</span></p>
			<br>
			<span class="percent">
				<input type="number" bind:value={tip} min="0" max="100" readonly={!calcNotFinished} style="width:300px;" />
			</span>
			<br>
			<br>
		{:else}
			<p class="large"><Fa icon={faHeart} color="#2759DB" /> Feeling generous? <span class="highlight pointer" on:click="{invertTip}" >Add a tip</span></p>
		{/if}

		{#if calcNotFinished}
			<button class="btn btn-primary" on:click={splitMoney}>Split!</button>
			<button class="btn" on:click={resetValues}>Reset</button>
		{:else}
			{#if errored}
				<p class="error">The amount, number of people, and tip must all be valid numbers!</p>
				<button class="btn" on:click={resetValues}>Reset</button>
			{:else}
				<p class="x-large">Everyone's share is: <span class="highlight">${total}</span></p>
				<button class="btn" on:click={resetValues}>Reset</button>
				<button class="btn btn-primary" on:click={copyText}><Fa icon={faShareAlt} color="white" /></button>
			{/if}
		{/if}
	</div>
</main>