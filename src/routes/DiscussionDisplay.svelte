<script lang="ts">
	// DiscussionDisplay.svelte

	import type { InfiniteEvent } from "svelte-infinite-loading/types/index"

	import InfiniteLoading from "svelte-infinite-loading"
	import ChatBubble from "./ChatBubble.svelte"
	import { fetchGet } from "$lib/global"

	export let currentDiscussionId: number // To detect change of current conversation
	export let new_message: string

	let displayed_messages: string[] = []
	let message_pool: string[] = []
	for (let i = 0; i < 1000; ++i) {
		message_pool.push(`Message #${i}`)
	}

	const initial_load = 10
	let load_error: boolean
	async function switchMessages(_currentDiscussionId: typeof currentDiscussionId) {
		const api: string = `/api/chans/${currentDiscussionId}/messages`
		load_error = false
		let fetched_messages
		try {
			const response = await fetchGet(api, { nMessages: initial_load })
			fetched_messages = await response.json()
		} catch (err: any) {
			load_error = true
			console.log("DiscussionDisplay", fetched_messages)
			console.error("DiscussionDisplay", "Could not fetch conversation:", err.message)
			return
		}
		if (_currentDiscussionId === currentDiscussionId) displayed_messages = fetched_messages
	}

	const reactivity = 1
	const loading_greediness = 10
	function infiniteHandler(e: InfiniteEvent) {
		const {
			detail: { loaded, complete },
		} = e
		new Promise((resolve) => {
			let tmp: string[] = []
			for (let i = 0; i < loading_greediness && message_pool.length > 0; ++i) {
				tmp.push(message_pool.pop() as string)
			}
			resolve(tmp)
		}).then((fetched_messages) => {
			if (
				Array.isArray(fetched_messages) &&
				fetched_messages.every((item) => typeof item === "string")
			) {
				if (fetched_messages.length > 0) {
					displayed_messages = [...fetched_messages, ...displayed_messages]
					loaded()
				} else {
					complete()
				}
			}
		})
	}

	$: {
		switchMessages(currentDiscussionId)
	}

	function handleNewMessage(_new_message: typeof new_message) {
		if (_new_message) {
			displayed_messages = [_new_message, ...displayed_messages]
		}
	}

	$: {
		handleNewMessage(new_message)
	}
</script>

<!-- The normal flexbox inside a reverse flexbox is a trick to scroll to the bottom when the element loads -->
<div class="flex flex-col-reverse space-y-4 overflow-y-auto p-4">
	<div class="flex flex-col">
		{#if !displayed_messages?.length}
			<p class="text-center font-semibold">This conversation has not started yet</p>
		{:else if load_error}
			<p class="text-center font-semibold">
				We encountered an error trying to retrieve this conversation's messages. Please
				check again your internet connection and refresh the page
			</p>
		{:else}
			<InfiniteLoading on:infinite={infiniteHandler} direction="top" distance={reactivity}>
				<!-- <div slot="noResults"></div> -->
				<!-- <div slot="noMore"></div> -->
				<div slot="error">
					Ooops, something went wrong when trying to fecth previous messages
				</div>
			</InfiniteLoading>
			{#each displayed_messages as message}
				<ChatBubble
					from_me={Math.random() < 0.5}
					from={["alice", "bob", "chet", "denis"][Math.floor(Math.random() * 4)]}
				>
					{message}
				</ChatBubble>
			{/each}
		{/if}
	</div>
</div>
