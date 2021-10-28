<script context="module">
	/**
	 * @type {import('@sveltejs/kit').load}
	 */
	export async function load({ fetch }) {
		const res = await fetch('https://deckofcardsapi.com/api/deck/new/shuffle/?deck_count=1');
		const data = await res.json();

		const cardRes = await fetch(
			`https://deckofcardsapi.com/api/deck/${data.deck_id}/draw/?count=52`
		);
		const cardData = await cardRes.json();

		if (cardData) {
			return {
				props: {
					cards: cardData.cards
				}
			};
		}

		return {
			error: new Error('could not load')
		};
	}
</script>

<script>
	import Card from '../components/Card.svelte';

	export let cards;

	let first = { code: '' };
	let second = { code: '' };
	let matched = false;

	function getColor(card) {
		if (card.suit === 'DIAMONDS' || card.suit === 'HEARTS') return 'red';
		return 'black';
	}

	function setCards(card) {
		if (!first.code) {
			first = card;
		} else if (!second.code && card.code !== first.code) {
			second = card;
		} else if (first.value === second.value && getColor(first) === getColor(second)) {
			matched = true;
		} else {
			first = { code: '' };
			second = { code: '' };
		}
	}

	function resetCards() {
		first = { code: '' };
		second = { code: '' };
		matched = false;
	}
</script>

<div class="grid grid-cols-12 gap-4 p-8 min-h-screen max-w-7xl mx-auto">
	{#each cards as card}
		<div on:click={setCards(card)}>
			<Card
				{card}
				selected={card.code === first.code || card.code === second.code}
				{matched}
				{resetCards}
			/>
		</div>
	{/each}
</div>
