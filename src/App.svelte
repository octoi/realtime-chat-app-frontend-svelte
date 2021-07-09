<script>
	import { onMount } from "svelte";

	let username = "username";
	let messages = [];
	let message = "";

	onMount(() => {
		const appId = __myenv.env.PUSHER_APP_ID;
		const cluster = __myenv.env.PUSHER_CLUSTER;

		Pusher.logToConsole = true;

		var pusher = new Pusher(appId, {
			cluster: cluster,
		});

		var channel = pusher.subscribe("my-channel");
		channel.bind("my-event", function (data) {
			alert(JSON.stringify(data));
		});
	});

	const submitHandler = (e) => {
		e.preventDefault();
		message = "";
	};
</script>

<div class="container">
	<div class="d-flex flex-column align-items-stretch flex-shrink-0 bg-white">
		<div
			class="d-flex align-items-center flex-shrink-0 p-3 link-dark text-decoration-none border-bottom"
		>
			<input
				type="text"
				class="fs-5 fw-semibold"
				placeholder="username"
				bind:value={username}
			/>
		</div>
		<div class="list-group list-group-flush border-bottom scrollarea">
			{#each messages as msg}
				<div
					class="list-group-item list-group-item-action py-3 lh-tight"
				>
					<div
						class="d-flex w-100 align-items-center justify-content-between"
					>
						<strong class="mb-1">{msg.username}</strong>
					</div>
					<div class="col-10 mb-1 small">{msg.message}</div>
				</div>
			{/each}
		</div>
	</div>
	<form on:submit={submitHandler}>
		<input
			type="text"
			class="form-control"
			bind:value={message}
			placeholder="Enter message"
		/>
	</form>
</div>

<style>
	.scrollarea {
		min-height: 500px;
	}
</style>
