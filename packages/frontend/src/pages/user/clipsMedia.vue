<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
		<div class="_spacer" style="--MI_SPACER-w: 1200px;">
		<div :class="$style.tabPadding">
				<MkTab v-model="tab" class="tab">
					<option :value="null">{{ i18n.ts.clip }}</option>
					<option value="grid">{{ i18n.ts.grid }}</option>
				</MkTab>
			</div>
			<div>
				<MkPagination v-slot="{items}" ref="list" :paginator="paginator">
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
	</div>
</template>

<script lang="ts" setup>
import { computed, ref, markRaw } from 'vue';
import { i18n } from '@/i18n.js';
import * as Misskey from 'misskey-js';
import MkPagination from '@/components/MkPagination.vue';
import MkClipsMedias from '@/components/MkClipsMedias.vue';
import MkTab from '@/components/MkTab.vue';
import {Paginator} from "@/utility/paginator";

const tab = ref<string | null>(null);
const props = defineProps<{
	user: Misskey.entities.User;
}>();

const paginator = markRaw(new Paginator('users/clips', {
	limit: 20,
	computedParams: computed(() => ({
		userId: props.user.id,
	})),
}));
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
	grid-template-columns: 1fr 1fr;
	grid-auto-rows: 1fr;
	grid-gap: 6px;
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
