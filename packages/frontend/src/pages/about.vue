<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<PageWithHeader v-model:tab="tab" :actions="headerActions" :tabs="headerTabs">
	<MkHorizontalSwipe v-model:tab="tab" :tabs="headerTabs">
		<MkSpacer v-if="tab === 'overview'" :contentMax="600" :marginMin="20">
			<XOverview/>
		</MkSpacer>
		<MkSpacer v-else-if="tab === 'emojis'" :contentMax="1000" :marginMin="20">
			<XEmojis/>
		</MkSpacer>
		<MkSpacer v-else-if="instance.federation !== 'none' && tab === 'federation'" :contentMax="1000" :marginMin="20">
			<XFederation/>
		</MkSpacer>
		<MkSpacer v-else-if="tab === 'charts'" :contentMax="1000" :marginMin="20">
			<MkInstanceStats/>
		</MkSpacer>
	</MkHorizontalSwipe>
</PageWithHeader>
</template>

<script lang="ts" setup>
import { computed, defineAsyncComponent, ref, watch } from 'vue';
import { instance } from '@/instance.js';
import { i18n } from '@/i18n.js';
import { claimAchievement } from '@/utility/achievements.js';
import { definePage } from '@/page.js';
import MkHorizontalSwipe from '@/components/MkHorizontalSwipe.vue';

const XOverview = defineAsyncComponent(() => import('@/pages/about.overview.vue'));
const XEmojis = defineAsyncComponent(() => import('@/pages/about.emojis.vue'));
const XFederation = defineAsyncComponent(() => import('@/pages/about.federation.vue'));
const MkInstanceStats = defineAsyncComponent(() => import('@/components/MkInstanceStats.vue'));

const props = withDefaults(defineProps<{
	initialTab?: string;
}>(), {
	initialTab: 'overview',
});

const tab = ref(props.initialTab);

watch(tab, () => {
	if (tab.value === 'charts') {
		claimAchievement('viewInstanceChart');
	}
});

const headerActions = computed(() => []);

const headerTabs = computed(() => {
	const items = [];

	items.push({
		key: 'overview',
		title: i18n.ts.overview,
	}, {
		key: 'emojis',
		title: i18n.ts.customEmojis,
		icon: 'ti ti-icons',
	});

	if (instance.federation !== 'none') {
		items.push({
			key: 'federation',
			title: i18n.ts.federation,
			icon: 'ti ti-whirl',
		});
	}

	items.push({
		key: 'charts',
		title: i18n.ts.charts,
		icon: 'ti ti-chart-line',
	});

	return items;
});

definePage(() => ({
	title: i18n.ts.instanceInfo,
	icon: 'ti ti-info-circle',
}));
</script>
