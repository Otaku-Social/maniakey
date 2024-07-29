<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<template v-for="file in note.files">
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
	note: Misskey.entities.Note;
}>();
</script>

<style lang="scss" module>
.img {
	position: relative;
	height: 115px;
	border-radius: 6px;
	overflow: clip;
}

@media (min-width: 720px) {
	.img {
		height: 180px;
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
