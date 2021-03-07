<script>

	import { onMount } from 'svelte';
	import _ from 'lodash';
	import moment from 'moment';
	export let name;
	const BASE_URL = `https://hacker-news.firebaseio.com/v0/`;
	let ids = [];
	let stories = [];
	// let promise = new Promise.resolve([]);


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
			console.log('result', result);
			
			const date = result.time ? Date(moment.unix(result.time).format('YYYY-MM-DD')) : '-';
			const url = result.url ? result.url : '-';
			// console.log(url, date);


			const formattedData = {
				title: result.title,
				score: result.score,
				url,
				date
			}
			return formattedData;
		});

		return mapStory;
	}


	const storyResponse = async (array) => {
		let topStories = await getTopStories(array);
		await Promise.all(topStories).then((values) => {
			console.log('values',values)
			stories = values;
			console.log(' stories', stories);
		});
		console.log('stories??', stories);
		// return values;
}

	onMount(async () => {
		const res = await getIDs();
		ids = await res;
		console.log('ids', ids);
		await storyResponse(ids);
		console.log('final status of stories', stories);

		// return stories;

		
	})



</script>

<main>
	<h1>{name}!</h1>
{#each stories as story}
<div>
	<h2>{story.title}</h2>
	<div class = "data">
	<p id="score"><span>Score: </span>{story.score} </p>
	<p id="date"><span> Date: </span>{story.date}</p>
</div>
<p id="url">({story.url})</p>



</div>
{:else}
<p>waiting..</p>
	
{/each}


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
	h2 {
		padding: 0;
		margin-top: 3%;
		margin-bottom: 0;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
	span{
		font-style: italic;
	}
	.data{
		display: flex;
		justify-content: center;
		margin:auto;
		padding: 0px 10px;
		
	}
	#url{
		font-size: .8rem;
		margin:auto;
	}
</style>