<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkSpacer :contentMax="1200">
		<MkInfo>この機能は実験的なものです。将来的に仕様変更される可能性があります。</MkInfo>
		<div>
			<MkPagination v-slot="{items}" ref="list" :pagination="pagination">
				<div v-for="item in items">
					<MkA :key="item.id" :to="`/clips/${item.id}`" :class="$style.item" class="_panel _margin">
						<div :class="$style.title">
							<b>{{ item.name }}</b>
						</div>
						<div v-if="item.description" :class="$style.description">{{ item.description }}</div>
						<MkClipsMedias :clipId="item.id"/>
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
import MkInfo from "@/components/MkInfo.vue";

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
</style>
