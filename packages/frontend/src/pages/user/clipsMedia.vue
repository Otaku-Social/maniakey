<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkSpacer :contentMax="1200">
		<div>
			<MkPagination v-slot="{items}" ref="list" :pagination="pagination">
				<div v-for="item in items">
					<MkA :key="item.id" :to="`/clips/${item.id}`" :class="$style.item" class="_panel _margin">
						<div :class="$style.title">
							<b>{{ item.name }}</b>
						</div>
						<div v-if="item.description" :class="$style.description">{{ item.description }}</div>
						<div :class="$style.medias">
							<MkClipsMedias :clipId="item.id"/>
						</div>
					</MkA>
				</div>
			</MkPagination>
		</div>
	</MkSpacer>
</template>

<script lang="ts" setup>
import {computed} from 'vue';
import * as Misskey from 'misskey-js';
import MkPagination from '@/components/MkPagination.vue';
import MkClipsMedias from '@/components/MkClipsMedias.vue';
import MkSpacer from "@/components/global/MkSpacer.vue";

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
	padding: 16px;
}

.title {
	font-size: 1.1rem;
}

.description {
	padding-top: 8px;
}

.medias {
	padding-top: 8px;
	height: 130px;
	width: 100%;
	overflow: hidden;
}

@media (min-width: 720px) {
	.medias {
		height: 180px;
	}
}
</style>
