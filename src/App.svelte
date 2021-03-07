<script>


	import _ from 'lodash';
	export let name;
	const BASE_URL = `https://hacker-news.firebaseio.com/v0/`;
	let ids = [];
	let stories = [];
	// let storiesPromise = new Promise.resolve([]);


	async function getIDs(){
		try {
			const response = await fetch(`${BASE_URL}/topstories.json?print=pretty`);
			let fullData = await response.json();
			ids = fullData;
			ids.length = 30;
			console.log(ids);
			return ids;

		} catch (error) {
			console.log(error, 'problem getting IDs');
		}
	}



	let getStories = getIDs()
	.then((ids) => {
		console.log('ids', ids);
		const storyInfo = _.map(ids, async (id) => {
			const response = await fetch(`${BASE_URL}/item/${id}.json?print=pretty`);
			const result = await response.json();
			// stories.push(result);
			return result;
		})
		// console.log('storyInfo--', storyInfo);
		return storyInfo;
	})
	getStories.then( (story) => {
		 Promise.all(story).then((res)=> {
			console.log('res------', res)
			stories = res;
			console.log('stories--------->>>>>', stories);
			return stories;
		})
		// stories = res;
		// return stories;
	})
	console.log('STORIES', stories)

	// let dataObject = {
	// 	title: '',
	// 	score: '',
	// 	url: '',
	// 	rank: ''
	// }

	

</script>

<main>
	<h1>{name}!</h1>
	{#await getStories}
	<p>...waiting.</p>
		
	{:then stories}
	<h2>{stories}</h2>
		{#each stories as {story}}
			<p>{story}</p>
		{/each}

		
	{/await}


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