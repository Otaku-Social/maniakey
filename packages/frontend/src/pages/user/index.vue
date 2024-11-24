<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<MkStickyContainer>
	<template #header><MkPageHeader v-model:tab="tab" :actions="headerActions" :tabs="headerTabs"/></template>
	<div>
		<div v-if="user">
			<MkHorizontalSwipe v-model:tab="tab" :tabs="headerTabs">
				<XHome v-if="tab === 'home'" key="home" :user="user"/>
				<MkSpacer v-else-if="tab === 'notes'" key="notes" :contentMax="800" style="padding-top: 0">
					<XTimeline :user="user"/>
				</MkSpacer>
				<XGalleryFromPosts v-else-if="tab === 'galleryFromPosts'" key="galleryFromPosts" :user="user"/>
				<XClipsMedia v-else-if="tab === 'clipsMedia'" key="clipsMedia" :user="user"/>
				<XAchievements v-else-if="tab === 'achievements'" key="achievements" :user="user"/>
				<XReactions v-else-if="tab === 'reactions'" key="reactions" :user="user"/>
        <XMore v-else-if="tab === 'more'" key="more" :user="user"/>
      </MkHorizontalSwipe>
		</div>
		<MkError v-else-if="error" @retry="fetchUser()"/>
		<MkLoading v-else/>
	</div>
</MkStickyContainer>
</template>

<script lang="ts" setup>
import {computed, defineAsyncComponent, ref, watch} from 'vue';
import * as Misskey from 'misskey-js';
import {acct as getAcct} from '@/filters/user.js';
import {misskeyApi} from '@/scripts/misskey-api.js';
import {definePageMetadata} from '@/scripts/page-metadata.js';
import {i18n} from '@/i18n.js';
import {$i} from '@/account.js';
import MkHorizontalSwipe from '@/components/MkHorizontalSwipe.vue';
import { getServerContext } from '@/server-context.js';

const XHome = defineAsyncComponent(() => import('./home.vue'));
const XTimeline = defineAsyncComponent(() => import('./index.timeline.vue'));
const XClipsMedia = defineAsyncComponent(() => import('./clipsMedia.vue'));
const XAchievements = defineAsyncComponent(() => import('./achievements.vue'));
const XReactions = defineAsyncComponent(() => import('./reactions.vue'));
const XGalleryFromPosts = defineAsyncComponent(() => import('./post-gallery.vue'));
const XMore = defineAsyncComponent(() => import('./more.vue'));

const CTX_USER = getServerContext('user');

const props = withDefaults(defineProps<{
	acct: string;
	page?: string;
	user: Misskey.entities.UserDetailed;
}>(), {
	page: 'home',
});

const tab = ref(props.page);

const user = ref<null | Misskey.entities.UserDetailed>(CTX_USER);
const error = ref<any>(null);

function fetchUser(): void {
	if (props.acct == null) return;

	const { username, host } = Misskey.acct.parse(props.acct);

	if (CTX_USER && CTX_USER.username === username && CTX_USER.host === host) {
		user.value = CTX_USER;
		return;
	}

	user.value = null;
	misskeyApi('users/show', {
		username,
		host,
	}).then(u => {
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
}, ...(user.value.host == null ? [{
	key: 'clipsMedia',
	title: i18n.ts.clips,
	icon: 'ti ti-paperclip',
}] : []), ...(user.value.host == null ? [{
	key: 'achievements',
	title: i18n.ts.achievements,
	icon: 'ti ti-medal',
}] : []), ...($i && ($i.id === user.value.id || $i.isAdmin || $i.isModerator)) || user.value.publicReactions ? [{
	key: 'reactions',
	title: i18n.ts.reaction,
	icon: 'ti ti-mood-happy',
}] : [], ...(user.value.host == null ? [{
	key: 'more',
	title: i18n.ts.more,
	icon: 'ti ti-menu',
}] : [])
] : []);

definePageMetadata(() => ({
	title: i18n.ts.user,
	icon: 'ti ti-user',
	...user.value ? {
		title: user.value.name ? `${user.value.name} (@${user.value.username})` : `@${user.value.username}`,
		subtitle: `@${getAcct(user.value)}`,
		userName: user.value,
		avatar: user.value,
		path: `/@${user.value.username}`,
		share: {
			title: user.value.name,
		},
	} : {},
}));
</script>
