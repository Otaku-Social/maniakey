<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<div class="_spacer" style="--MI_SPACER-w: 1500px;">
		<div :class="$style.root">
				<MkSwitch v-model="nsfwNoConfirm">センシティブをモザイクなしで表示する</MkSwitch>
				<MkSwitch v-model="moreColumn">列を増やして表示する</MkSwitch>
			</div>
			<div :class="$style.root">
				<MkPagination v-slot="{items}" :pagination="pagination">
					<div :class="[moreColumn ? $style.moreColumnsStream : $style.stream]">
						<MkMedias v-for="note in items" :note="note" :nsfwNoConfirm="nsfwNoConfirm" :moreColumn="moreColumn"/>
					</div>
				</MkPagination>
			</div>
	</div>
</template>

<script lang="ts" setup>

import MkMedias from "@/components/MkMedias.vue";
import MkPagination from "@/components/MkPagination.vue";
import {ref, computed, onMounted} from "vue";
import * as Misskey from "misskey-js";
import MkSwitch from "@/components/MkSwitch.vue";

const props = defineProps<{
	user: Misskey.entities.UserDetailed;
}>();

const nsfwNoConfirm = ref<boolean>(false);
const moreColumn = ref<boolean>(false);

const pagination = {
	endpoint: 'users/notes' as const,
	limit: 10,
	params: computed(() => ({
		userId: props.user.id,
		withFiles: true,
	})),
};
</script>

<style lang="scss" module>
.root {
	padding: 8px;
	display: flex;
	flex-direction: column;
	gap: 0.75em;
}

.stream {
	display: grid;
	grid-template-columns: 1fr 1fr 1fr;
	grid-auto-rows: 1fr;
	grid-gap: 6px;
}

.moreColumnsStream {
	display: grid;
	grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
	grid-auto-rows: 1fr;
	grid-gap: 6px;
}

@media (min-width: 720px) {
	.stream {
		grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
	}

	.moreColumnsStream {
		grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr 1fr;
	}
}
</style>
