<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkPaginationNoMessage v-slot="{items}" ref="list" :paginator="paginator">
		<div v-if="items.length > 0">
			<!-- サムネイル形式 -->
			<div v-if="props.grid" :class="$style.streamGrid">
				<div v-for="(file, fileIndex) in getAllFiles(items)">
					<div v-if="fileIndex === 0">
						<div v-if="file.type.startsWith('video')" :class="$style.imgGrid" style="background-color: #2d2d2d">
							<div :class="$style.sensitive">
								<div><i class="ti ti-movie"></i> {{ i18n.ts.video }}</div>
							</div>
						</div>
						<div v-else-if="file.isSensitive" :class="$style.imgGrid">
							<ImgWithBlurhash :class="$style.sensitiveImg" :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"
															 :forceBlurhash="true"/>
							<div :class="$style.sensitive">
								<div>
									<div><i class="ti ti-eye-exclamation"></i> {{ i18n.ts.sensitive }}</div>
								</div>
							</div>
						</div>
						<div v-else :class="$style.imgGrid">
							<ImgWithBlurhash :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"/>
						</div>
					</div>
				</div>
			</div>

			<!-- リスト形式 -->
			<div v-else :class="$style.stream">
				<div v-for="file in getAllFiles(items)">
					<div v-if="file.type.startsWith('video')" :class="$style.img" style="background-color: #2d2d2d">
						<div :class="$style.sensitive">
							<div><i class="ti ti-movie"></i> {{ i18n.ts.video }}</div>
						</div>
					</div>
					<div v-else-if="file.isSensitive" :class="$style.img">
						<ImgWithBlurhash :class="$style.sensitiveImg" :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"
														 :forceBlurhash="true"/>
						<div :class="$style.sensitive">
							<div>
								<div><i class="ti ti-eye-exclamation"></i> {{ i18n.ts.sensitive }}</div>
							</div>
						</div>
					</div>
					<div v-else :class="$style.img">
						<ImgWithBlurhash :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"/>
					</div>
				</div>
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
import { computed, markRaw } from 'vue';
import MkPaginationNoMessage from "@/components/MkPaginationNoMessage.vue";
import { i18n } from "@/i18n.js";
import ImgWithBlurhash from "@/components/MkImgWithBlurhash.vue";
import * as Misskey from "misskey-js";
import { store } from "@/store.js";
import { getStaticImageUrl } from "@/utility/media-proxy.js";
import { Paginator } from '@/utility/paginator.js';

function thumbnail(image: Misskey.entities.DriveFile): string {
	return store.s.disableShowingAnimatedImages
		? getStaticImageUrl(image.url)
		: image.thumbnailUrl;
}

function getAllFiles(items: Misskey.entities.Note[]) {
	if (!items || items.length === 0) {
		return [];
	}
	return items.flatMap((note) => note.files).slice(0, 6);
}

const props = defineProps<{
	grid: boolean;
	clipId: string;
}>();

const paginator = markRaw(new Paginator('clips/file-notes', {
	limit: props.grid ? 1 : 6,
	computedParams: computed(() => ({
		clipId: props.clipId,
	})),
}));

</script>

<style lang="scss" module>

.stream {
	padding-top: 8px;
	width: 100%;
	overflow: hidden;
	display: grid;
	grid-template-columns: repeat(3, 1fr);
	grid-template-rows: auto;
	grid-gap: 6px;
}

.streamGrid {
	padding-top: 8px;
	grid-template-columns: 1fr;
	grid-auto-rows: 1fr;
	overflow: clip;
}

.warn {
	border-radius: 6px;
	aspect-ratio: 1/1;
	position: relative;
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

.img {
	aspect-ratio: 1/1;
	position: relative;
	border-radius: 6px;
	overflow: clip;
}

.imgGrid {
	aspect-ratio: 1/1;
	position: relative;
	border-radius: 6px;
	overflow: clip;
	background-color: #ffffff;
}

.empty {
	margin: 0;
	padding: 16px;
	text-align: center;
}

.sensitiveImg {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	filter: brightness(0.7);
}

.sensitive {
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
		grid-template-columns: repeat(6, 1fr);
	}
}

</style>
