<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<PageWithAnimBg>
	<div :class="$style.formContainer">
		<div :class="$style.form">
			<div v-if="$i.policies.canCreateAccessToken">
				<MkAuthConfirm
					ref="authRoot"
					:name="name"
					:icon="icon || undefined"
					:permissions="_permissions"
					@accept="onAccept"
					@deny="onDeny"
				>
					<template #consentAdditionalInfo>
						<div v-if="callback != null" class="_gaps_s" :class="$style.redirectRoot">
							<div>{{ i18n.ts._auth.byClickingYouWillBeRedirectedToThisUrl }}</div>
							<div class="_monospace" :class="$style.redirectUrl">{{ callback }}</div>
						</div>
					</template>
				</MkAuthConfirm>
			</div>
			<div v-else>
				<MkInfo warn>{{ i18n.ts.noPermissionToUseMiAuth }}</MkInfo>
			</div>
		</div>
	</div>
</PageWithAnimBg>
</template>

<script lang="ts" setup>
import { computed, useTemplateRef } from 'vue';
import * as Misskey from 'misskey-js';
import MkAuthConfirm from '@/components/MkAuthConfirm.vue';
import { i18n } from '@/i18n.js';
import { $i } from '@/i.js';
import { misskeyApi } from '@/utility/misskey-api.js';
import { definePage } from '@/page.js';
import MkInfo from "@/components/MkInfo.vue";

const props = defineProps<{
	session: string;
	callback?: string;
	name?: string;
	icon?: string;
	permission?: string; // コンマ区切り
}>();

const _permissions = computed(() => {
	return (props.permission ? props.permission.split(',').filter((p): p is typeof Misskey.permissions[number] => (Misskey.permissions as readonly string[]).includes(p)) : []);
});

const authRoot = useTemplateRef('authRoot');

async function onAccept(token: string) {
	await misskeyApi('miauth/gen-token', {
		session: props.session,
		name: props.name,
		iconUrl: props.icon,
		permission: _permissions.value,
	}, token).then(() => {
		if (props.callback && props.callback !== '') {
			const cbUrl = new URL(props.callback);
			if (['javascript:', 'file:', 'data:', 'mailto:', 'tel:', 'vbscript:'].includes(cbUrl.protocol)) throw new Error('invalid url');
			cbUrl.searchParams.set('session', props.session);
			window.location.href = cbUrl.toString();
		} else {
			authRoot.value?.showUI('success');
		}
	}).catch(() => {
		authRoot.value?.showUI('failed');
	});
}

function onDeny() {
	authRoot.value?.showUI('denied');
}

definePage(() => ({
	title: 'MiAuth',
	icon: 'ti ti-apps',
}));
</script>

<style lang="scss" module>
.formContainer {
	min-height: 100svh;
	padding: 32px 32px calc(env(safe-area-inset-bottom, 0px) + 32px) 32px;
	box-sizing: border-box;
	display: grid;
	place-content: center;
}

.form {
	position: relative;
	z-index: 10;
	border-radius: var(--MI-radius);
	background-color: var(--MI_THEME-panel);
	box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
	overflow: clip;
	max-width: 500px;
	width: calc(100vw - 64px);
	height: min(65svh, calc(100svh - calc(env(safe-area-inset-bottom, 0px) + 64px)));
	overflow-y: scroll;
}

.redirectRoot {
	padding: 16px;
	border-radius: var(--MI-radius);
	background-color: var(--MI_THEME-bg);
}

.redirectUrl {
	font-size: 90%;
	padding: 12px;
	border-radius: var(--MI-radius);
	background-color: var(--MI_THEME-panel);
	overflow-x: scroll;
	white-space: nowrap;
}
</style>
