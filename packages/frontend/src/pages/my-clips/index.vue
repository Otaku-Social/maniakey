<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<PageWithHeader v-model:tab="tab" :actions="headerActions" :tabs="headerTabs" :swipable="true">
		<div class="_spacer _gaps" style="--MI_SPACER-w: 1200px;">
			<MkTip k="clips">
				{{ i18n.ts._clip.tip }}
			</MkTip>
			<div v-if="tab === 'my'" class="_gaps">
				<MkButton primary rounded class="add" @click="create"><i class="ti ti-plus"></i> {{ i18n.ts.add }}</MkButton>

				<MkPagination v-slot="{ items }" :paginator="paginator" class="_gaps" withControl>
					<MkClipPreview v-for="item in items" :key="item.id" :clip="item" :noUserInfo="true"/>
				</MkPagination>
			</div>
			<div v-else-if="tab === 'favorites'">
				<MkPagination v-slot="{ items }" :paginator="favoritesPaginator" class="_gaps" withControl>
					<MkClipPreview v-for="item in items" :key="item.id" :clip="item" :noUserInfo="true"/>
				</MkPagination>
			</div>
			<div v-else-if="tab === 'my-beta'" class="_gaps">
				<MkButton primary rounded class="add" @click="create"><i class="ti ti-plus"></i> {{ i18n.ts.add }}</MkButton>

				<MkPagination v-slot="{ items }" :paginator="paginator" class="_gaps" withControl>
					<div :class="$style.gridContainer">
						<div v-for="item in items">
							<MkA :key="item.id" :to="`/clips/${item.id}`">
								<MkClipsMedias :clipId="item.id" :grid="true" />
								<div :class="$style.titleGrid">
									<b>{{ item.name }}</b>
								</div>
							</MkA>
						</div>
					</div>
				</MkPagination>
			</div>
			<div v-else-if="tab === 'favorites-beta'">
				<MkPagination v-slot="{ items }" :paginator="favoritesPaginator" class="_gaps" withControl>
					<div :class="$style.gridContainer">
						<div v-for="item in items">
							<MkA :key="item.id" :to="`/clips/${item.id}`">
								<MkClipsMedias :clipId="item.id" :grid="true" />
								<div :class="$style.titleGrid">
									<b>{{ item.name }}</b>
								</div>
							</MkA>
							<div :class="$style.userInfo">
								<MkAvatar :user="item.user" :class="$style.userAvatar" indicator link preview/> <MkUserName :user="item.user" :nowrap="false"/>
							</div>
						</div>
					</div>
				</MkPagination>
			</div>

		</div>
	</PageWithHeader>
</template>

<script lang="ts" setup>
import { watch, ref, computed, markRaw } from 'vue';
import * as Misskey from 'misskey-js';
import MkPagination from '@/components/MkPagination.vue';
import MkButton from '@/components/MkButton.vue';
import MkClipPreview from '@/components/MkClipPreview.vue';
import MkClipsMedias from '@/components/MkClipsMedias.vue';
import * as os from '@/os.js';
import { i18n } from '@/i18n.js';
import { definePage } from '@/page.js';
import { clipsCache } from '@/cache.js';
import { Paginator } from '@/utility/paginator.js';
import number from "@/filters/number";
import { $i } from "@/i";

const tab = ref('my');

const paginator = markRaw(new Paginator('clips/list', {
}));

const favoritesPaginator = markRaw(new Paginator('clips/my-favorites', {
}));

async function create() {
	const { canceled, result } = await os.form(i18n.ts.createNewClip, {
		name: {
			type: 'string',
			label: i18n.ts.name,
		},
		description: {
			type: 'string',
			required: false,
			multiline: true,
			treatAsMfm: true,
			label: i18n.ts.description,
		},
		isPublic: {
			type: 'boolean',
			label: i18n.ts.public,
			default: false,
		},
	});

	if (canceled) return;

	os.apiWithDialog('clips/create', result);

	clipsCache.delete();

	paginator.reload();
}

function onClipCreated() {
	paginator.reload();
}

function onClipDeleted() {
	paginator.reload();
}

const headerActions = computed(() => []);

const headerTabs = computed(() => [{
	key: 'my',
	title: i18n.ts.myClips,
	icon: 'ti ti-paperclip',
}, {
	key: 'favorites',
	title: i18n.ts.favorites,
	icon: 'ti ti-heart',
},
	{
		key: 'my-beta',
		title: i18n.ts.myClips + " (Grid)",
		icon: 'ti ti-paperclip',
	},
	{
		key: 'favorites-beta',
		title: i18n.ts.favorites + " (Grid)",
		icon: 'ti ti-heart',
	}
]);

definePage(() => ({
	title: i18n.ts.clip,
	icon: 'ti ti-paperclip',
}));
</script>

<style lang="scss" module>
.titleGrid {
	padding-top: 3px;
	font-size: 1.05rem;
}

.gridContainer {
	width: 100%;
	display: grid;
	grid-template-columns: 1fr 1fr;
	grid-auto-rows: 1fr;
	grid-gap: 6px;
}

.userInfo {
	padding-top: 4px;
}

.userAvatar {
	width: 24px;
	height: 24px;
}

@media (min-width: 720px) {
	.titleGrid {
		padding-top: 8px;
		font-size: 1.1rem;
	}

	.gridContainer {
		grid-template-columns: 1fr 1fr  1fr 1fr;
	}
}
</style>
