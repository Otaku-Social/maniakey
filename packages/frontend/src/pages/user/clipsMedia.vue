<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkSpacer :contentMax="1200">
		<div :class="$style.tabPadding">
			<MkTab v-model="tab" class="tab">
				<option :value="null">{{ i18n.ts.clip }}</option>
				<option value="grid">{{ i18n.ts.grid }}</option>
			</MkTab>
		</div>
		<div>
			<MkPagination v-slot="{items}" ref="list" :pagination="pagination">
				<div v-if="tab === null">
					<div v-for="item in items">
							<MkA :key="item.id" :to="`/clips/${item.id}`" :class="$style.item" class="_panel _margin">
								<div :class="$style.title">
									<b>{{ item.name }}</b>
								</div>
								<div v-if="item.description" :class="$style.description">{{ item.description }}</div>
								<MkClipsMedias :clipId="item.id" :grid="false" />
							</MkA>
					</div>
				</div>
				<div v-if="tab === 'grid'" :class="$style.gridContainer">
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
	</MkSpacer>
</template>

<script lang="ts" setup>
import {computed, ref} from 'vue';
import { i18n } from '@/i18n.js';
import * as Misskey from 'misskey-js';
import MkPagination from '@/components/MkPagination.vue';
import MkClipsMedias from '@/components/MkClipsMedias.vue';
import MkSpacer from "@/components/global/MkSpacer.vue";
import MkInfo from "@/components/MkInfo.vue";
import MkTab from '@/components/MkTab.vue';

const tab = ref<string | null>(null);
const props = defineProps<{
	user: Misskey.entities.User;
}>();

const pagination = {
	endpoint: 'users/clips' as const,
	limit: 20,
	params: computed(() => ({
		userId: props.user.id,
	})),
};
</script>

<style lang="scss" module>
.item {
	display: block;
	padding: 10px;
}

.title {
	font-size: 1.1rem;
}

.titleGrid {
	padding-top: 3px;
	font-size: 1.05rem;
}

.description {
	padding-top: 8px;
}

.tab {
	padding: calc(var(--margin) / 2) 0;
	background: var(--bg);
}

.tabPadding {
	padding: 5px 0;
}

.gridContainer {
	width: 100%;
	display: grid;
	grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
	grid-gap: 3px;
}

@media (min-width: 720px) {
	.titleGrid {
		padding-top: 8px;
		font-size: 1.1rem;
	}

	.gridContainer {
		grid-template-columns: repeat(auto-fill, minmax(260px, 1fr));
		grid-gap: 8px;
	}
}
</style>
