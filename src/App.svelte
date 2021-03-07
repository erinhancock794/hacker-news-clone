<script>

	import { onMount } from 'svelte';
	import _ from 'lodash';
	import moment from 'moment';
	export let name;
	const BASE_URL = `https://hacker-news.firebaseio.com/v0/`;
	let ids = [];
	let stories = [];
	let promise = Promise.resolve([]);

	// let storiesPromise = new Promise.resolve([]);

	async function getIDs(){
		try {
			const response = await fetch(`${BASE_URL}/topstories.json?print=pretty`);
			let fullData = await response.json();
			ids = fullData;
			ids.length = 30;
			return ids;

		} catch (error) {
			console.log(error, 'problem getting IDs');
		}
	}

	async function getTopStories(ids) {
		console.log('getTopStories is hit----', ids);
		const mapStory = _.map(ids, async (id) => {
			const response = await fetch(`${BASE_URL}/item/${id}.json?print=pretty`);
			const result = await response.json();
			
			const date = Date(moment.unix(result.time))

			const formattedData = {
				title: result.title,
				score: result.score,
				url: result.url,
				date
			}
			return formattedData;
		});

		console.log('mapStory', mapStory);
		return mapStory;
	}


	const storyResponse = async (array) => {
		await getTopStories(array).then(() => {
		Promise.all((values) => {
			stories = values;
			return stories;
		})
	})
}

	onMount(async () => {
		const res = await getIDs();
		ids = await res;
		console.log('ids', ids);
		promise = storyResponse(ids);
		stories = promise;
		console.log('final status of promise', promise);
		console.log('final status of stories', stories);

		return promise;

		
	})



</script>

<main>
	<h1>{name}!</h1>
<!-- {#each stories as story}
<p>{story}</p>
{:else}
<p>waiting..</p>
	
{/each} -->


</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>