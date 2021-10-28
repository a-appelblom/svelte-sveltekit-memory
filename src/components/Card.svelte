<script>
	import { afterUpdate } from 'svelte';

	export let card;
	export let selected = false;
	export let matched = false;
	export let resetCards;

	let locked = false;

	const joker = 'https://deckofcardsapi.com/static/img/X1.png';

	afterUpdate(() => {
		if (selected && matched) {
			locked = true;
			resetCards();
		}
	});
</script>

<div>
	{#if selected || locked}
		<img src={card.image} alt="" />
	{:else}
		<img src={joker} alt="" />
	{/if}
</div>
