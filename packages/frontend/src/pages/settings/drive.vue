<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<SearchMarker path="/settings/drive" :label="i18n.ts.drive" :keywords="['drive']" icon="ti ti-cloud">
	<div class="_gaps_m">
		<SearchMarker :keywords="['capacity', 'usage']">
			<FormSection first>
				<template #label><SearchLabel>{{ i18n.ts.usageAmount }}</SearchLabel></template>

				<div v-if="!fetching" class="_gaps_m">
					<div>
						<div :class="$style.meter"><div :class="$style.meterValue" :style="meterStyle"></div></div>
					</div>
					<FormSplit>
						<MkKeyValue>
							<template #key>{{ i18n.ts.capacity }}</template>
							<template #value>{{ bytes(capacity, 1) }}</template>
						</MkKeyValue>
						<MkKeyValue>
							<template #key>{{ i18n.ts.inUse }}</template>
							<template #value>{{ bytes(usage, 1) }}</template>
						</MkKeyValue>
						<MkKeyValue>
							<template #key>{{ i18n.ts.free }}</template>
							<template #value>{{ bytes(capacity - usage, 1) }}</template>
						</MkKeyValue>
					</FormSplit>
				</div>
			</FormSection>
		</SearchMarker>

		<SearchMarker :keywords="['statistics', 'usage']">
			<FormSection>
				<template #label><SearchLabel>{{ i18n.ts.statistics }}</SearchLabel></template>
				<MkChart src="per-user-drive" :args="{ user: $i }" span="day" :limit="7 * 5" :bar="true" :stacked="true" :detailed="false" :aspectRatio="6"/>
			</FormSection>
		</SearchMarker>

		<FormSection>
			<div class="_gaps_m">
				<SearchMarker :keywords="['default', 'upload', 'folder']">
					<FormLink @click="chooseUploadFolder()">
						<SearchLabel>{{ i18n.ts.uploadFolder }}</SearchLabel>
						<template #suffix>{{ uploadFolder ? uploadFolder.name : '-' }}</template>
						<template #suffixIcon><i class="ti ti-folder"></i></template>
					</FormLink>
				</SearchMarker>

				<FormLink to="/settings/drive/cleaner">
					{{ i18n.ts.drivecleaner }}
				</FormLink>

				<SearchMarker :keywords="['keep', 'original', 'raw', 'upload']">
					<MkSwitch v-model="keepOriginalUploading">
						<template #label><SearchLabel>{{ i18n.ts.keepOriginalUploading }}</SearchLabel></template>
						<template #caption><SearchKeyword>{{ i18n.ts.keepOriginalUploadingDescription }}</SearchKeyword></template>
					</MkSwitch>
				</SearchMarker>

				<SearchMarker :keywords="['keep', 'original', 'filename']">
					<MkSwitch v-model="keepOriginalFilename">
						<template #label><SearchLabel>{{ i18n.ts.keepOriginalFilename }}</SearchLabel></template>
						<template #caption><SearchKeyword>{{ i18n.ts.keepOriginalFilenameDescription }}</SearchKeyword></template>
					</MkSwitch>
				</SearchMarker>

				<SearchMarker :keywords="['always', 'default', 'mark', 'nsfw', 'sensitive', 'media', 'file']">
					<MkSwitch v-model="alwaysMarkNsfw" @update:modelValue="saveProfile()">
						<template #label><SearchLabel>{{ i18n.ts.alwaysMarkSensitive }}</SearchLabel></template>
					</MkSwitch>
				</SearchMarker>

				<SearchMarker :keywords="['auto', 'nsfw', 'sensitive', 'media', 'file']">
					<MkSwitch v-model="autoSensitive" @update:modelValue="saveProfile()">
						<template #label><SearchLabel>{{ i18n.ts.enableAutoSensitive }}</SearchLabel><span class="_beta">{{ i18n.ts.beta }}</span></template>
						<template #caption><SearchKeyword>{{ i18n.ts.enableAutoSensitiveDescription }}</SearchKeyword></template>
					</MkSwitch>
				</SearchMarker>
			</div>
		</FormSection>
	</div>
</SearchMarker>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue';
import * as Misskey from 'misskey-js';
import tinycolor from 'tinycolor2';
import FormLink from '@/components/form/link.vue';
import MkSwitch from '@/components/MkSwitch.vue';
import FormSection from '@/components/form/section.vue';
import MkKeyValue from '@/components/MkKeyValue.vue';
import FormSplit from '@/components/form/split.vue';
import * as os from '@/os.js';
import { misskeyApi } from '@/scripts/misskey-api.js';
import bytes from '@/filters/bytes.js';
import { defaultStore } from '@/store.js';
import MkChart from '@/components/MkChart.vue';
import { i18n } from '@/i18n.js';
import { definePageMetadata } from '@/scripts/page-metadata.js';
import { signinRequired } from '@/account.js';

const $i = signinRequired();

const fetching = ref(true);
const usage = ref<number | null>(null);
const capacity = ref<number | null>(null);
const uploadFolder = ref<Misskey.entities.DriveFolder | null>(null);
const alwaysMarkNsfw = ref($i.alwaysMarkNsfw);
const autoSensitive = ref($i.autoSensitive);

const meterStyle = computed(() => {
	if (!capacity.value || !usage.value) return {};
	return {
		width: `${usage.value / capacity.value * 100}%`,
		background: tinycolor({
			h: 180 - (usage.value / capacity.value * 180),
			s: 0.7,
			l: 0.5,
		}).toHslString(),
	};
});

const keepOriginalUploading = computed(defaultStore.makeGetterSetter('keepOriginalUploading'));
const keepOriginalFilename = computed(defaultStore.makeGetterSetter('keepOriginalFilename'));

misskeyApi('drive').then(info => {
	capacity.value = info.capacity;
	usage.value = info.usage;
	fetching.value = false;
});

if (defaultStore.state.uploadFolder) {
	misskeyApi('drive/folders/show', {
		folderId: defaultStore.state.uploadFolder,
	}).then(response => {
		uploadFolder.value = response;
	});
}

function chooseUploadFolder() {
	os.selectDriveFolder(false).then(async folder => {
		defaultStore.set('uploadFolder', folder[0] ? folder[0].id : null);
		os.success();
		if (defaultStore.state.uploadFolder) {
			uploadFolder.value = await misskeyApi('drive/folders/show', {
				folderId: defaultStore.state.uploadFolder,
			});
		} else {
			uploadFolder.value = null;
		}
	});
}

function saveProfile() {
	misskeyApi('i/update', {
		alwaysMarkNsfw: !!alwaysMarkNsfw.value,
		autoSensitive: !!autoSensitive.value,
	}).catch(err => {
		os.alert({
			type: 'error',
			title: i18n.ts.error,
			text: err.message,
		});
		alwaysMarkNsfw.value = true;
	});
}

const headerActions = computed(() => []);

const headerTabs = computed(() => []);

definePageMetadata(() => ({
	title: i18n.ts.drive,
	icon: 'ti ti-cloud',
}));
</script>

<style lang="scss" module>
.meter {
	height: 10px;
	background: rgba(0, 0, 0, 0.1);
	border-radius: 999px;
	overflow: clip;
}

.meterValue {
	height: 100%;
	border-radius: 999px;
}
</style>
