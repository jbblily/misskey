<!--
SPDX-FileCopyrightText: syuilo and misskey-project
SPDX-License-Identifier: AGPL-3.0-only
-->

<template>
<section>
	<div v-if="app.permission.length > 0">
		<p>{{ i18n.tsx._auth.permission({ name }) }}</p>
		<ul>
			<li v-for="p in app.permission" :key="p">{{ i18n.ts._permissions[p] }}</li>
		</ul>
	</div>
	<div>{{ i18n.tsx._auth.shareAccess({ name: `${name} (${app.id})` }) }}</div>
	<div :class="$style.buttons">
		<MkButton inline @click="cancel">{{ i18n.ts.cancel }}</MkButton>
		<MkButton inline primary @click="accept">{{ i18n.ts.accept }}</MkButton>
	</div>
</section>
</template>

<script lang="ts" setup>
import { computed } from 'vue';
import * as Misskey from 'misskey-js';
import MkButton from '@/components/MkButton.vue';
import { misskeyApi } from '@/utility/misskey-api.js';
import { i18n } from '@/i18n.js';

const props = defineProps<{
	session: Misskey.entities.AuthSessionShowResponse;
}>();

const emit = defineEmits<{
	(event: 'accepted'): void;
	(event: 'denied'): void;
}>();

const app = computed(() => props.session.app);

const name = computed(() => {
	const el = window.document.createElement('div');
	el.textContent = app.value.name;
	return el.innerHTML;
});

function cancel() {
	misskeyApi('auth/deny', {
		token: props.session.token,
	}).then(() => {
		emit('denied');
	});
}

function accept() {
	misskeyApi('auth/accept', {
		token: props.session.token,
	}).then(() => {
		emit('accepted');
	});
}
</script>

<style lang="scss" module>
.buttons {
	margin-top: 16px;
	display: flex;
	gap: 8px;
	flex-wrap: wrap;
}
</style>
