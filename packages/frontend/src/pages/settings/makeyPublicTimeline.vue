<!--
SPDX-FileCopyrightText: Morikapu @tmorio
This source code is based on existing implementations.
I would like to express my gratitude to all Misskey developers and contributors.
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<div class="_gaps_m">
	<MkInfo>これらの設定をオンにしてもノートの公開範囲によっては公開タイムラインに掲載されます。画面に反映するにはリロードしてください。</MkInfo>
	<div v-if="instance.policies.ltlAvailable && $i.policies.ltlAvailable">
		<MkSwitch v-model="makeyHideLocalTimeline" @update:model-value="save()">ローカル・ソーシャルを非表示にする</MkSwitch>
	</div>
	<div v-if="instance.policies.gtlAvailable && $i.policies.gtlAvailable">
		<MkSwitch v-model="makeyHideFederatedTimeline" @update:model-value="save()">グローバルを非表示にする</MkSwitch>
	</div>
	<MkButton primary @click="reloadPage"><i class="ti ti-refresh"></i>リロードして反映する</MkButton>
</div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue';
import MkSwitch from '@/components/MkSwitch.vue';
import MkButton from '@/components/MkButton.vue';
import MkInfo from '@/components/MkInfo.vue';
import { $i } from '@/i.js';
import { instance } from '@/instance.js';
import { definePageMetadata } from '@/scripts/page-metadata.js';
import { misskeyApi } from "@/scripts/misskey-api.js";

let makeyHideLocalTimeline = ref($i.makeyHideLocalTimeline);
let makeyHideFederatedTimeline = ref($i.makeyHideFederatedTimeline);


function save() {
  misskeyApi('i/update', {
    makeyHideLocalTimeline: !!makeyHideLocalTimeline.value,
    makeyHideFederatedTimeline: !!makeyHideFederatedTimeline.value,
  })
}

async function reloadPage() {
	location.reload();
}

const headerActions = computed(() => []);

const headerTabs = computed(() => []);

definePageMetadata({
	title: '公開タイムライン',
	icon: 'ti ti-world',
});
</script>
