<script lang="ts">
	import ButtonScrollToBottom from '$lib/components/ButtonScrollToBottom.svelte';
	import { Button } from '$lib/components/ui/button';
	import { IconRefresh, IconStop } from '$lib/components/ui/icons';
	import type { UseChatHelpers } from 'ai/svelte';
	import FooterText from '$lib/components/FooterText.svelte';
	import PromptForm from '$lib/components/PromptForm.svelte';
	import { writable, type Writable } from 'svelte/store';
	import { getFn } from 'vitest/dist/suite.js';
	import type { Post, PostStore, usePosts } from '../../stores/posts';

	let id: string;
	let messages: Post[];

	export let posts: ReturnType<typeof usePosts>;
	let isLoading = writable(false);
	export let input: Writable<string>;

	$: {
		if ($posts) {
			id = posts.getUUID();
			messages = $posts.chats[id];
		}
	}

	const stop = () => {
		// posts.stop();
	};
	const reload = () => {
		// posts.reload();
	};

	const gen = ({ input, files }: { input: string; files: File[] }) => {
		console.log('input', input);
		console.log('files', files);
		posts.generate(input, files);
	};
</script>

<div class="fixed inset-x-0 bottom-0 bg-gradient-to-b from-muted/10 from-10% to-muted/30 to-50%">
	<ButtonScrollToBottom />
	<div class="mx-auto sm:max-w-2xl sm:px-4">
		<div class="flex h-10 items-center justify-center">
			{#if isLoading}
				<Button variant="outline" on:click={() => stop()} class="bg-background">
					<IconStop class="mr-2" />
					Stop generating
				</Button>
			{:else if messages?.length > 0}
				<Button variant="outline" on:click={() => reload()} class="bg-background">
					<IconRefresh class="mr-2" />
					Regenerate response
				</Button>
			{/if}
		</div>
		<div
			class="space-y-4 border-t bg-background px-4 py-2 shadow-lg sm:rounded-t-xl sm:border md:py-4"
		>
			<PromptForm
				on:submit={async (event) => {
					gen(event.detail);
				}}
				{isLoading}
			/>
		</div>
	</div>
</div>
