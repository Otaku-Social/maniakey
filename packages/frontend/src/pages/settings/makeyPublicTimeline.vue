<template>
<div class="_gaps_m">
	<MkSwitch v-model="makeyHideLocalTimeline" @update:model-value="save()">ローカル・ソーシャルを非表示にする<template #caption>この設定をオンにしてもノートの公開範囲によっては公開タイムラインに掲載されます。この設定を画面に反映するにはリロードする必要があります。</template></MkSwitch>
	<MkSwitch v-model="makeyHideFederatedTimeline" @update:model-value="save()">グローバルを非表示にする<template #caption>この設定をオンにしてもノートの公開範囲によっては公開タイムラインに掲載されます。この設定を画面に反映するにはリロードする必要があります。</template></MkSwitch>
</div>
</template>

<script lang="ts" setup>
import { } from 'vue';
import MkSwitch from '@/components/MkSwitch.vue';
import MkSelect from '@/components/MkSelect.vue';
import FormSection from '@/components/form/section.vue';
import MkFolder from '@/components/MkFolder.vue';
import * as os from '@/os';
import { defaultStore } from '@/store';
import { i18n } from '@/i18n';
import { $i } from '@/account';
import { definePageMetadata } from '@/scripts/page-metadata';

let makeyHideLocalTimeline = $ref($i.makeyHideLocalTimeline);
let makeyHideFederatedTimeline = $ref($i.makeyHideFederatedTimeline);


function save() {
	os.api('i/update', {
		makeyHideLocalTimeline: !!makeyHideLocalTimeline,
		makeyHideFederatedTimeline: !!makeyHideFederatedTimeline,
	});
}

const headerActions = $computed(() => []);

const headerTabs = $computed(() => []);

definePageMetadata({
	title: '公開タイムライン',
	icon: 'ti ti-world',
});
</script>
