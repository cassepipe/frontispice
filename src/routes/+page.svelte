<script lang="ts">
	/* Components */
	import DiscussionDisplay from "./DiscussionDisplay.svelte"
	import CreateDiscussion from "./CreateDiscussion.svelte"
	import ChatBox from "./ChatBox.svelte"

	let new_message: string
	function messageSentHandler(e: CustomEvent<typeof new_message>) {
		console.log("You sent a message:", e.detail[0])
		new_message = e.detail
	}

	let discussions: string[] = []
	for (let i = 0; i < 12; ++i) {
		discussions.push(`Discussion #${i}`)
	}

	let currentDiscussionId: number = 0
</script>

<!-- Horizontal grid -->
<div class="grid grid-cols-[auto_1fr]" id="wrapper">
	<!-- Vertical grid 1-->
	<div class="both grid grid-rows-[1fr_auto]" id="convos">
		<section class="p-4">
			<CreateDiscussion />
		</section>
		<section class="overflow-y-auto">
			{#each discussions as d}
				<div class="variant-form-material h-16 p-4 font-bold">
					{d}
				</div>
			{/each}
		</section>
	</div>

	<!-- Vertical grid 2-->
	<div class="both grid grid-rows-[1fr_auto]" id="messages">
		<!-- Messages -->
		<DiscussionDisplay {new_message} {currentDiscussionId} />

		<!-- Input box -->
		<section class="border-t border-black p-4">
			<ChatBox on:message_sent={messageSentHandler} {currentDiscussionId} />
		</section>
	</div>
</div>

<style lang="postcss">
	#wrapper {
		height: calc(100vh - 78px);
	}

	#messages {
		height: calc(100vh - 78px);
	}

	#convos {
		height: calc(100vh - 78px);
	}
</style>
