<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkPagination ref="pagingComponent" :pagination="pagination" :disableAutoLoad="disableAutoLoad">
		<template #empty>
			<div class="_fullinfo">
				<img :src="infoImageUrl" class="_ghost"/>
				<div>{{ i18n.ts.noNotes }}</div>
			</div>
		</template>

		<template #default="{ items: notes }">
			<div :class="[$style.root, { [$style.noGap]: noGap }]" v-slot="{ item: note }">
				<MkDateSeparatedList
					ref="notes"
					v-slot="{ item: note }"
					:items="notes"
					:direction="pagination.reversed ? 'up' : 'down'"
					:reversed="pagination.reversed"
					:noGap="noGap"
					:ad="true"
					:class="$style.notes"
				>
					<MkNote :key="note._featuredId_ || note._prId_ || note.id" :class="$style.note" :note="note"/>
				</MkDateSeparatedList>
			</div>
		</template>
	</MkPagination>
</template>

<!--<template>
	<div v-if="!fetching && files.length > 0" :class="$style.stream">
		<template v-for="file in files" :key="file.note.id + file.file.id">
			<div v-if="file.file.isSensitive && !showingFiles.includes(file.file.id)" :class="$style.sensitive" @click="showingFiles.push(file.file.id)">
				<div>
					<div><i class="ti ti-eye-exclamation"></i> {{ i18n.ts.sensitive }}</div>
					<div>{{ i18n.ts.clickToShow }}</div>
				</div>
			</div>
			<MkA v-else :class="$style.img" :to="notePage(file.note)">
				&lt;!&ndash; TODO: 画像以外のファイルに対応 &ndash;&gt;
				<ImgWithBlurhash :hash="file.file.blurhash" :src="thumbnail(file.file)" :title="file.file.name"/>
			</MkA>
		</template>
	</div>
	<p v-if="!fetching && files.length == 0" :class="$style.empty">{{ i18n.ts.nothing }}</p>
</template>-->

<script lang="ts" setup>
import { shallowRef } from 'vue';
import MkNote from '@/components/MkNote.vue';
import MkDateSeparatedList from '@/components/MkDateSeparatedList.vue';
import MkPagination, { Paging } from '@/components/MkPagination.vue';
import { i18n } from '@/i18n.js';
import { infoImageUrl } from '@/instance.js';

const props = defineProps<{
	pagination: Paging;
	noGap?: boolean;
	disableAutoLoad?: boolean;
}>();

const pagingComponent = shallowRef<InstanceType<typeof MkPagination>>();

defineExpose({
	pagingComponent,
});
</script>

<style lang="scss" module>
.root {
	&.noGap {
		> .notes {
			background: var(--panel);
		}
	}

	&:not(.noGap) {
		> .notes {
			background: var(--bg);

			.note {
				background: var(--panel);
				border-radius: var(--radius);
			}
		}
	}
}
</style>

<!--
<script lang="ts" setup>
import { onMounted } from 'vue';
import * as Misskey from 'misskey-js';
import { getStaticImageUrl } from '@/scripts/media-proxy.js';
import { notePage } from '@/filters/note.js';
import * as os from '@/os.js';
import ImgWithBlurhash from '@/components/MkImgWithBlurhash.vue';
import { defaultStore } from '@/store.js';
import { i18n } from '@/i18n.js';
import {infoImageUrl} from "@/instance.js";
import MkNote from "@/components/MkNote.vue";
import MkPagination from "@/components/MkPagination.vue";
import MkDateSeparatedList from "@/components/MkDateSeparatedList.vue";

const props = defineProps<{
	user: Misskey.entities.UserDetailed;
}>();

let fetching = $ref(true);
let files = $ref<{
	note: Misskey.entities.Note;
	file: Misskey.entities.DriveFile;
}[]>([]);
let showingFiles = $ref<string[]>([]);

function thumbnail(image: Misskey.entities.DriveFile): string {
	return defaultStore.state.disableShowingAnimatedImages
		? getStaticImageUrl(image.url)
		: image.thumbnailUrl;
}

</script>

<style lang="scss" module>
.root {
	padding: 8px;
}

.stream {
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
	grid-gap: 6px;
}

.img {
	height: 200px;
	border-radius: 6px;
	overflow: clip;
}

@media (max-width: 568px) {
	.stream {
		grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
	}

	.img {
		height: 120px;
	}
}

.empty {
	margin: 0;
	padding: 16px;
	text-align: center;
}

.sensitive {
	display: grid;
	place-items: center;
}
</style>
-->
