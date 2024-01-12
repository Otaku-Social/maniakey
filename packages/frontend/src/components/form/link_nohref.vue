<!--
SPDX-FileCopyrightText: syuilo and other misskey contributors
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<div :class="[$style.root, { [$style.inline]: inline }]">
	<div v-if="external" :class="$style.main" class="_button" target="_blank">
		<span :class="$style.icon"><slot name="icon"></slot></span>
		<span :class="$style.text"><slot></slot></span>
		<span :class="$style.suffix">
			<span :class="$style.suffixText"><slot name="suffix"></slot></span>
			<i class="ti ti-external-link"></i>
		</span>
	</div>
	<div v-else :class="[$style.main, { [$style.active]: active }]" class="_button" :behavior="behavior">
		<span :class="$style.icon"><slot name="icon"></slot></span>
		<span :class="$style.text"><slot></slot></span>
		<span :class="$style.suffix">
			<span :class="$style.suffixText"><slot name="suffix"></slot></span>
			<i class="ti ti-chevron-right"></i>
		</span>
	</div>
</div>
</template>

<script lang="ts" setup>
import { } from 'vue';

const props = defineProps<{
	active?: boolean;
	external?: boolean;
	behavior?: null | 'window' | 'browser';
	inline?: boolean;
}>();
</script>

<style lang="scss" module>
.root {
	display: block;

	&.inline {
		display: inline-block;
	}
}

.main {
	display: flex;
	align-items: center;
	width: 100%;
	box-sizing: border-box;
	padding: 10px 14px;
	background: var(--buttonBg);
	border-radius: 6px;
	font-size: 0.9em;

	&:hover {
		text-decoration: none;
		background: var(--buttonHoverBg);
	}

	&.active {
		color: var(--accent);
		background: var(--buttonHoverBg);
	}
}

.icon {
	margin-right: 0.75em;
	flex-shrink: 0;
	text-align: center;
	color: var(--fgTransparentWeak);

	&:empty {
		display: none;

		& + .text {
			padding-left: 4px;
		}
	}
}

.text {
	flex-shrink: 1;
	white-space: normal;
	padding-right: 12px;
	text-align: center;
}

.suffix {
	margin-left: auto;
	opacity: 0.7;
	white-space: nowrap;

	> .suffixText:not(:empty) {
		margin-right: 0.75em;
	}
}
</style>
