<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkPaginationNoMessage v-slot="{items}" ref="list" :pagination="pagination">
		<div :class="$style.stream" v-if="items.length > 0">
			<MkClipsNotesWithMedia v-for="note in items" :note="note"/>
		</div>
	</MkPaginationNoMessage>
</template>

<script lang="ts" setup>
import {computed} from 'vue';
import MkClipsNotesWithMedia from "@/components/MkClipsNotesWithMedia.vue";
import MkPaginationNoMessage from "@/components/MkPaginationNoMessage.vue";

const props = defineProps<{
	clipId: string;
}>();

const pagination = {
	endpoint: 'clips/file-notes' as const,
	limit: 5,
	params: computed(() => ({
		clipId: props.clipId,
	})),
};
</script>

<style lang="scss" module>

.stream {
	padding-top: 8px;
	height: 115px;
	width: 100%;
	overflow: hidden;
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
	grid-gap: 6px;
}

@media (min-width: 720px) {
	.stream {
		height: 180px;
		grid-template-columns: repeat(auto-fill, minmax(190px, 1fr));
	}
}

</style>
