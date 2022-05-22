<script>
	class SPARQLQueryDispatcher {
		constructor(endpoint) {
			this.endpoint = endpoint;
		}

		query(sparqlQuery) {
			const fullUrl = this.endpoint + '?query=' + encodeURIComponent(sparqlQuery);
			const headers = { Accept: 'application/sparql-results+json' };

			return fetch(fullUrl, { headers }).then((body) => body.json());
		}
	}

	const endpointUrl = 'https://query.wikidata.org/sparql';
	let producerQID = 'Q2510603';
	$: sparqlQuery = `SELECT ?album ?albumLabel WHERE {
SERVICE wikibase:label { bd:serviceParam wikibase:language "en". }
      ?album p:P162 ?statement0.
      ?statement0 (ps:P162/(wdt:P279*)) wd:${producerQID}.
      ?album p:P31 ?statement1.
      ?statement1 (ps:P31/(wdt:P279*)) wd:Q482994.
  }`;

	const queryDispatcher = new SPARQLQueryDispatcher(endpointUrl);

	let response = null;

	function getChain() {
		response = queryDispatcher.query(sparqlQuery);
		console.log(producerQID);
		console.log(sparqlQuery);
	}

	getChain();
</script>

<h1>You can never break the chain</h1>
<input bind:value={producerQID} /><button on:click={getChain}>the chain</button>

{#await response}
	waiting
	{typeof response}
{:then albums}
	<ul>
		{#each albums.results.bindings as album}
			<li><a href={album.album.value}>{album.albumLabel.value}</a></li>
		{/each}
	</ul>
{:catch error}
	Oh no!
{/await}
