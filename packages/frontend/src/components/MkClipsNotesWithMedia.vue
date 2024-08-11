<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<template v-for="(file, index) in note.files">
		<div v-if="props.grid && index == 0">
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
			<div v-else :class="$style.imgGrid" >
				<ImgWithBlurhash :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"/>
			</div>
		</div>
		<div v-else-if="!props.grid">
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
			<div v-else :class="$style.img" >
				<ImgWithBlurhash :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"/>
			</div>
		</div>
	</template>
</template>

<script lang="ts" setup>

import {i18n} from "@/i18n.js";
import ImgWithBlurhash from "@/components/MkImgWithBlurhash.vue";
import * as Misskey from "misskey-js";
import {defaultStore} from "@/store.js";
import {getStaticImageUrl} from "@/scripts/media-proxy.js";


function thumbnail(image: Misskey.entities.DriveFile): string {
	return defaultStore.state.disableShowingAnimatedImages
		? getStaticImageUrl(image.url)
		: image.thumbnailUrl;
}

const props = defineProps<{
	grid: boolean;
	note: Misskey.entities.Note;
}>();
</script>

<style lang="scss" module>
.img {
	position: relative;
	height: 110px;
	border-radius: 6px;
	overflow: clip;
}

.imgGrid {
	position: relative;
	height: 160px;
	border-radius: 6px;
	overflow: clip;
	background-color: #ffffff;
}

@media (min-width: 720px) {
	.img {
		height: 180px;
	}

	.imgGrid {
		height: 250px;
	}
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
</style>
