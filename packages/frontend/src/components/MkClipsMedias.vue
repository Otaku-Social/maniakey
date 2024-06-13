<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkPagination v-slot="{items}" ref="list" :pagination="pagination">
		<div :class="$style.stream">
			<MkClipsNotesWithMedia v-for="note in items" :note="note"/>
		</div>
	</MkPagination>
</template>

<script lang="ts" setup>
import {computed} from 'vue';
import MkClipsNotesWithMedia from "@/components/MkClipsNotesWithMedia.vue";
import MkPagination from "@/components/MkPagination.vue";

const props = defineProps<{
	clipId: string;
}>();

const pagination = {
	endpoint: 'clips/notes' as const,
	limit: 10,
	params: computed(() => ({
		clipId: props.clipId,
	})),
};
</script>

<style lang="scss" module>

.stream {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
	grid-gap: 6px;
}

@media (min-width: 720px) {
	.stream {
		grid-template-columns: repeat(auto-fill, minmax(190px, 1fr));
	}
}

</style>
