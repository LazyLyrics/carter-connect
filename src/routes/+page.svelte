<script lang="ts">

	let pluginUrl: string;
	let apiKey: string;
	let output: any = null;
	let error: any = null;

	async function listWebooks() {
		const data = {
			key: apiKey,
			url: "test"
		}

		const addResponse = await fetch("https://api.carterlabs.ai/webhook/add", {
			method: "POST",
			headers: {
				"Content-Type": "application/json"
			},
			body: JSON.stringify(data)
		})

		if (!addResponse.ok) {
			error = "Error adding webhook: " + addResponse.status + addResponse.statusText;
			return;
		}

		const removeResponse = await fetch("https://api.carterlabs.ai/webhook/remove", {
			method: "POST",
			headers: {
				"Content-Type": "application/json"
			},
			body: JSON.stringify(data)
		})

		if (!removeResponse.ok) {
			error = "Error removing webhook: " + removeResponse.status + removeResponse.statusText;
			return;
		}

		const removeOutput = await removeResponse.json();
		output = removeOutput

	}

	async function addPlugin() {
			const data = {
				key: apiKey,
				url: pluginUrl
			}

			pluginUrl = "";
			apiKey = "";

			const response = await fetch("https://api.carterlabs.ai/webhook/add", {
				method: "POST",
				headers: {
					"Content-Type": "application/json"
				},
				body: JSON.stringify(data)
			})

			output = await response.json();
	}
	async function removePlugin() {
			const data = {
				key: apiKey,
				url: pluginUrl
			}

			pluginUrl = "";
			apiKey = "";

			const response = await fetch("https://api.carterlabs.ai/webhook/remove", {
				method: "POST",
				headers: {
					"Content-Type": "application/json"
				},
				body: JSON.stringify(data)
			})

			const outputData = await response.json();
			console.log(outputData)
			if (outputData.webhooks) {
				output = outputData
			} else {
				error = "No data returned. This may be an error."
			}
	}
</script>

<div class="flex w-full justify-center">
	<div class="flex flex-col items-center m-4 gap-4 md:w-1/2">
		<h1 class="text-2xl gradient-heading">Carter Connect</h1>
		<div class="flex flex-col gap-2">
			<h2>Add Your Plugin Here</h2>
			<p>Enter your API key, and the URL of the plugin you wish to add. No data is saved at any point.</p>
			<p>Use list to get a list of current webhooks. Note: This works by adding and removing a placeholder webhook and only your API key is required.</p>
		</div>
		<form action="" on:submit|preventDefault class="flex flex-col gap-2 w-full">
			<div>
				<label class="label" for="pluginUrl">URL</label>
				<input bind:value={pluginUrl} class="input" type="text" name="pluginUrl" id="pluginUrl">
			</div>
			<div>
				<label class="label" for="apiKey">API Key</label>
				<input bind:value={apiKey} class="input" type="password" name="apiKey" id="apiKey">
			</div>
			<div class="container">
				<button on:click={addPlugin} class="btn variant-filled-success box" type="submit">Add</button>
				<button on:click={listWebooks} class="btn variant-filled-tertiary box">List</button>
				<button on:click={removePlugin} class="btn variant-filled-error box">Remove</button>
			</div>
		</form>
		<h2>Output</h2>
		<p>
			{#if error}
			{error}
			{/if}
		</p>
		<ul>
			{#if output && output.success}
				{#each output.webhooks as url}
					<li>{url}</li>
				{/each}
			{/if}
		</ul>
	</div>
</div>

<style>
	.container {
  display: flex;
	justify-content: space-between;
	gap: 10rem;
}


.box:first-child { flex-grow: 1; flex-basis: 0 }

.box:last-child { flex-grow: 1; flex-basis: 0 }
</style>
