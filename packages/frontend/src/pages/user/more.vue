<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
	<MkSpacer :contentMax="720" :marginMin="20" :marginMax="32">
		<div class="_gaps_s" v-if="tab !== ''">
			<MkButton @click="tab = ''"><i class="ti ti-chevron-left"></i>戻る</MkButton>
		</div>
		<div class="_gaps_s" v-if="tab === ''">
			<FormLink @click="tab = 'activity'"><template #icon><i class="ti ti-chart-line"></i></template>{{ i18n.ts.activity }}</FormLink>
			<FormLink @click="tab = 'clips'"><template #icon><i class="ti ti-paperclip"></i></template>{{ i18n.ts.clips }}</FormLink>
			<FormLink @click="tab = 'lists'"><template #icon><i class="ti ti-list"></i></template>{{ i18n.ts.lists }}</FormLink>
			<FormLink @click="tab = 'pages'"><template #icon><i class="ti ti-news"></i></template>{{ i18n.ts.pages }}</FormLink>
			<FormLink @click="tab = 'flashs'"><template #icon><i class="ti ti-player-play"></i></template>Play</FormLink>
			<FormLink @click="tab = 'gallery'"><template #icon><i class="ti ti-icons"></i></template>{{ i18n.ts.gallery }}</FormLink>
			<!--<FormLink @click="tab = 'raw'"><template #icon><i class="ti ti-code"></i></template>Raw</FormLink>-->
		</div>
		<XActivity v-if="tab === 'activity'" :user="user_info"/>
		<XClips v-else-if="tab === 'clips'" :user="user_info"/>
		<XLists v-else-if="tab === 'lists'" :user="user_info"/>
		<XPages v-else-if="tab === 'pages'" :user="user_info"/>
		<XFlashs v-else-if="tab === 'flashs'" :user="user_info"/>
		<XGallery v-else-if="tab === 'gallery'" :user="user_info"/>
		<!--<XRaw v-else-if="tab === 'raw'" :user="user_info"/>-->
	</MkSpacer>
</template>

<script lang="ts" setup>
import { defineAsyncComponent, ref } from 'vue';
import * as Misskey from 'misskey-js';
import { i18n } from '@/i18n.js';
import FormLink from "@/components/form/link_nohref.vue";
import MkButton from "@/components/MkButton.vue";

const XActivity = defineAsyncComponent(() => import('./activity.vue'));
const XClips = defineAsyncComponent(() => import('./clips.vue'));
const XLists = defineAsyncComponent(() => import('./lists.vue'));
const XPages = defineAsyncComponent(() => import('./pages.vue'));
const XFlashs = defineAsyncComponent(() => import('./flashs.vue'));
const XGallery = defineAsyncComponent(() => import('./gallery.vue'));
//const XRaw = defineAsyncComponent(() => import('./raw.vue'));

const props = withDefaults(defineProps<{
	page?: string;
	user: Misskey.entities.UserDetailed;
}>(), {
	page: '',
});

const tab = ref(props.page);
const user_info = ref(props.user);

</script>
