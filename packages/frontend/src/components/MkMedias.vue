<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<!-- TODO: 画像以外のファイルは後で考慮 -->
<template>
  <template v-for="file in note.files">
    <div v-if="file.type.startsWith('video')"
				 :class="[moreColumn ? $style.imgMoreColumn : $style.img]" style="background-color: #2d2d2d">
      <MkA :class="$style.sensitive" :to="notePage(note)">
        <div>
          <div><i class="ti ti-movie"></i> {{ i18n.ts.video }}</div>
          <div>{{ i18n.ts.clickToShow }}</div>
        </div>
      </MkA>
    </div>
    <div v-else-if="file.isSensitive && !showingFiles.includes(file.id) && !nsfwNoConfirm"
				 :class="[moreColumn ? $style.imgMoreColumn : $style.img]"
         @click="showingFiles.push(file.id)">
      <ImgWithBlurhash :class="$style.sensitiveImg" :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"
                       :forceBlurhash="true"/>
      <div :class="$style.sensitive">
        <div>
          <div><i class="ti ti-eye-exclamation"></i> {{ i18n.ts.sensitive }}</div>
          <div>{{ i18n.ts.clickToShow }}</div>
        </div>
      </div>
    </div>
    <MkA v-else :class="[moreColumn ? $style.imgMoreColumn : $style.img]" :to="notePage(note)">
      <ImgWithBlurhash :hash="file.blurhash" :src="thumbnail(file)" :title="file.name"/>
    </MkA>
  </template>
</template>

<script lang="ts" setup>

import { ref } from "vue";
import { notePage } from "@/filters/note.js";
import { i18n } from "@/i18n.js";
import ImgWithBlurhash from "@/components/MkImgWithBlurhash.vue";
import * as Misskey from "misskey-js";
import { store } from "@/store.js";
import { getStaticImageUrl } from "@/utility/media-proxy.js";

let showingFiles = ref<string[]>([]);

function thumbnail(image: Misskey.entities.DriveFile): string {
	return store.s.disableShowingAnimatedImages
		? getStaticImageUrl(image.url)
		: image.thumbnailUrl;
}

const props = defineProps<{
	note: Misskey.entities.Note;
	nsfwNoConfirm: boolean;
	moreColumn: boolean;
}>();
</script>

<style lang="scss" module>
.img {
	aspect-ratio: 1/1;
	position: relative;
	border-radius: 6px;
	overflow: clip;
}

.imgMoreColumn {
	aspect-ratio: 1/1;
	position: relative;
	border-radius: 6px;
	overflow: clip;
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
