<script>
	import { onMount } from "svelte";
	import Pusher from "pusher-js";

	let username = "username";
	let messages = [];
	let message = "";

	onMount(() => {
		const appId = __myapp.env.PUSHER_APP_ID;
		const cluster = __myapp.env.PUSHER_CLUSTER;

		Pusher.logToConsole = true;

		const pusher = new Pusher(appId, {
			cluster: cluster,
		});

		const channel = pusher.subscribe("chat");
		channel.bind("message", (data) => {
			messages = [...messages, data];
		});
	});

	const submitHandler = async (e) => {
		e.preventDefault();

		await fetch("http://localhost:8000/api/messages", {
			method: "POST",
			headers: { "Content-Type": "application/json" },
			body: JSON.stringify({ username, message }),
		});

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
