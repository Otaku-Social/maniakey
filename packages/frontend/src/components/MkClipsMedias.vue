<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkPaginationNoMessage v-slot="{items}" ref="list" :pagination="pagination">
		<div v-if="items.length > 0">
			<div :class="$style.streamGrid" v-if="props.grid">
				<MkClipsNotesWithMedia v-for="note in items" :note="note" :grid="true" />
			</div>
			<div :class="$style.stream" v-else>
				<MkClipsNotesWithMedia v-for="note in items" :note="note" :grid="false" />
			</div>
		</div>
		<div v-if="items.length === 0 && props.grid">
			<div :class="$style.streamGrid">
				<div :class="$style.warn" style="background-color: #2d2d2d">
					<div :class="$style.info">
						<div><i class="ti ti-info-circle"></i> No Media</div>
					</div>
				</div>
			</div>
		</div>
	</MkPaginationNoMessage>
</template>

<script lang="ts" setup>
import {computed} from 'vue';
import MkClipsNotesWithMedia from "@/components/MkClipsNotesWithMedia.vue";
import MkPaginationNoMessage from "@/components/MkPaginationNoMessage.vue";

const props = defineProps<{
	grid: boolean;
	clipId: string;
}>();

const pagination = {
	endpoint: 'clips/file-notes' as const,
	limit: props.grid ? 1 : 5,
	params: computed(() => ({
		clipId: props.clipId,
	})),
};
</script>

<style lang="scss" module>

.stream {
	padding-top: 8px;
	height: 110px;
	width: 100%;
	overflow: hidden;
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
	grid-gap: 6px;
}

.streamGrid {
	padding-top: 8px;
	height: 160px;
	width: 100%;
	overflow: hidden;
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
	grid-gap: 6px;
}

.warn {
	position: relative;
	height: 160px;
	border-radius: 6px;
	overflow: clip;
}

.info {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	display: grid;
	place-items: center;
	font-size: 0.8em;
	color: #fff;
	cursor: pointer;
}

@media (min-width: 720px) {
	.stream {
		height: 180px;
		grid-template-columns: repeat(auto-fill, minmax(190px, 1fr));
	}

	.streamGrid {
		height: 250px;
		grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
	}

	.warn {
		height: 250px;
	}
}

</style>
