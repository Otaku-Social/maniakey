<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<MkStickyContainer>
	<template #header><MkPageHeader v-model:tab="tab" :actions="headerActions" :tabs="headerTabs"/></template>
	<div>
		<div v-if="user">
			<XHome v-if="tab === 'home'" :user="user"/>
			<MkSpacer v-else-if="tab === 'notes'" :contentMax="800" style="padding-top: 0">
				<XTimeline :user="user"/>
			</MkSpacer>
			<XGalleryFromPosts v-else-if="tab === 'galleryFromPosts'" :user="user"/>
			<XAchievements v-else-if="tab === 'achievements'" :user="user"/>
			<XReactions v-else-if="tab === 'reactions'" :user="user"/>
			<XMore v-else-if="tab === 'more'" :user="user"/>
		</div>
		<MkError v-else-if="error" @retry="fetchUser()"/>
		<MkLoading v-else/>
	</div>
</MkStickyContainer>
</template>

<script lang="ts" setup>
import { defineAsyncComponent, computed, watch, ref } from 'vue';
import * as Misskey from 'misskey-js';
import { acct as getAcct } from '@/filters/user.js';
import * as os from '@/os.js';
import { definePageMetadata } from '@/scripts/page-metadata.js';
import { i18n } from '@/i18n.js';
import { $i } from '@/account.js';

const XHome = defineAsyncComponent(() => import('./home.vue'));
const XTimeline = defineAsyncComponent(() => import('./index.timeline.vue'));
const XAchievements = defineAsyncComponent(() => import('./achievements.vue'));
const XReactions = defineAsyncComponent(() => import('./reactions.vue'));
const XGalleryFromPosts = defineAsyncComponent(() => import('./post-gallery.vue'));
const XMore = defineAsyncComponent(() => import('./more.vue'));

const props = withDefaults(defineProps<{
	acct: string;
	page?: string;
	user: Misskey.entities.UserDetailed;
}>(), {
	page: 'home',
});

const tab = ref(props.page);
const user = ref<null | Misskey.entities.UserDetailed>(null);
const error = ref<any>(null);

function fetchUser(): void {
	if (props.acct == null) return;
	user.value = null;
	os.api('users/show', Misskey.acct.parse(props.acct)).then(u => {
		user.value = u;
	}).catch(err => {
		error.value = err;
	});
}

watch(() => props.acct, fetchUser, {
	immediate: true,
});

const headerActions = computed(() => []);

const headerTabs = computed(() => user.value ? [{
	key: 'home',
	title: i18n.ts.overview,
	icon: 'ti ti-home',
}, {
	key: 'notes',
	title: i18n.ts.notes,
	icon: 'ti ti-pencil',
}, {
	key: 'galleryFromPosts',
	title: i18n.ts.galleryFromPost,
	icon: 'ti ti-icons',
},  ...(user.value.host == null ? [{
	key: 'achievements',
	title: i18n.ts.achievements,
	icon: 'ti ti-medal',
}] : []), ...($i && ($i.id === user.value.id)) || user.value.publicReactions ? [{
	key: 'reactions',
	title: i18n.ts.reaction,
	icon: 'ti ti-mood-happy',
}] : [], {
	key: 'more',
	title: i18n.ts.more,
	icon: 'ti ti-menu',
}] : []);

definePageMetadata(computed(() => user.value ? {
	icon: 'ti ti-user',
	title: user.value.name ? `${user.value.name} (@${user.value.username})` : `@${user.value.username}`,
	subtitle: `@${getAcct(user.value)}`,
	userName: user.value,
	avatar: user.value,
	path: `/@${user.value.username}`,
	share: {
		title: user.value.name,
	},
} : null));
</script>
