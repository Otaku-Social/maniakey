<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<MkModalWindow ref="dialog" :width="400" :height="450" :withOkButton="false" :okButtonDisabled="true" @close="dialog?.close()">
	<template #header>{{ i18n.ts.memo }}</template>

	<MkSpacer :class="$style.root" :marginMin="20" :marginMax="28">
		<div class="_gaps">
			<div v-for="(value, key) of memoList" :key="key" :class="$style.item">
				<div :class="$style.contents">
					<MkButton danger small :class="$style.button" @click="deleteMemo(key)"><i class="ti ti-trash"></i></MkButton>
					<div :class="$style.text">{{ value }}</div>
				</div>
				<MkButton small :disabled="key === props.widgetId" :class="$style.button" @click="selectMemo(key)">{{ key === props.widgetId ? i18n.ts.selected : i18n.ts.select }}</MkButton>
			</div>
		</div>
	</MkSpacer>
</MkModalWindow>
</template>

<script lang="ts" setup>
import { ref, shallowRef } from 'vue';
import { genId } from '@/utility/id.js';
import MkModalWindow from '@/components/MkModalWindow.vue';
import MkButton from '@/components/MkButton.vue';
import { i18n } from '@/i18n.js';
import { store } from '@/store.js';
import * as os from '@/os.js';

const memoList = ref({});

const getMemoList = () => {
	const memo = store.s.memo;
	if (!memo) return {};
	if (typeof memo === 'string') return { 'default': memo };
	return memo;
};

memoList.value = getMemoList();

const props = defineProps<{
	widgetId: string;
}>();

const dialog = shallowRef<InstanceType<typeof MkModalWindow>>();

const selectMemo = (key: string) => {
	memoList.value[genId()] = memoList.value[props.widgetId];
	memoList.value[props.widgetId] = memoList.value[key];
	delete memoList.value[key];
	store.set('memo', memoList.value);
	dialog.value?.close();
};

const deleteMemo = async (key: string) => {
	const confirm = await os.confirm({
		type: 'warning',
		text: i18n.ts.deleteConfirm,
	});
	if (confirm.canceled) return;

	delete memoList.value[key];
	store.set('memo', memoList.value);
};

</script>

<style lang="scss" module>
.root {
	background: var(--bg);
	height: 100%;
}

.item {
	display: flex;
	justify-content: space-between;
	align-items: center;
	background: var(--panel);
	padding: 14px;
	border-radius: 12px;
	gap: 6px;
}

.contents {
	display: flex;
	align-items: center;
	gap: 12px;
}

.button {
	min-width: auto;
	flex-shrink: 0;
}

.text {
	word-break: break-all;
}
</style>
