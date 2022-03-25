<template>
	<div>
		<div v-if="isState(SETUP_HINTS.SERVER_STATE_NOT_SETUP)" id="security-warning-state-warning">
			<span class="icon icon-error-white" /><span class="message">{{ t('richdocuments', 'Please configure a Collabora Online server to start editing documents') }}</span>
		</div>

		<div v-else
			:id="failureId">
			<div v-if="isState(SETUP_HINTS.SERVER_STATE_LOADING)">
				<span class="icon icon-loading" /><span class="message">{{ t('richdocuments', 'Checking the existing configuration') }}</span>
			</div>

			<div v-else-if="isState(SETUP_HINTS.SERVER_STATE_UPDATING)">
				<span class="icon icon-loading" /><span class="message">{{ t('richdocuments', 'Setting new server URL and checking the configuration') }}</span>
			</div>

			<div v-else-if="isState(SETUP_HINTS.SERVER_STATE_CONNECTION_ERROR)">
				<span class="icon icon-close-white" />
				<span class="message">
					{{ t('richdocuments', 'Could not establish connection to the Collabora Online server.') }}
					<span v-if="isNginx && serverMode === SERVER_MODE.BUILTIN">
						{{ t('richdocuments', 'This might be due to a missing configuration of your web server. For more information, please visit: ') }}
						<a title="Connecting Collabora Online Single Click with Nginx" class="external">{{ t('richdocuments', 'Connecting Collabora Online Single Click with Nginx') }}</a></span>
				</span>
			</div>

			<div v-else-if="isState(SETUP_HINTS.PROTOCOL_MISMATCH)">
				<span class="icon icon-close-white" /><span class="message">{{ t('richdocuments', 'Collabora Online should use the same protocol as the server installation.') }}</span>
			</div>
			<div v-else-if="isState(SETUP_HINTS.SERVER_STATE_CLIENT_CONNECTION_ERROR)">
				<span class="icon icon-close-white" /><span class="message">{{ t('richdocuments', 'Collabora Online is not reachable from the browser.') }}</span>
			</div>

			<div v-else>
				<span class="icon icon-checkmark-white" /><span class="message">{{ t('richdocuments', 'Collabora Online server is reachable.') }}</span>
			</div>
		</div>
	</div>
</template>

<script>

import { SETUP_HINTS, SERVER_MODE } from '../helpers/setupcheck'

export default {
	name: 'SetupHints',
	props: {
		settings: {
			type: Object,
		},
		serverError: {
			type: Number,
		},
		serverMode: {
			type: String,
		},
		isNginx: {
			type: Boolean,
		},
	},
	data() {
		return {
			SETUP_HINTS,
			SERVER_MODE,
		}
	},
	computed: {
		isState() {
			return (hint) => this.serverError === hint
		},
		failureId() {
			if (this.isState(SETUP_HINTS.SERVER_STATE_OK)) {
				return 'security-warning-state-ok'
			}

			return 'security-warning-state-failure'
		},
	},
}
</script>

<style scoped>
#security-warning-state-failure,
#security-warning-state-warning,
#security-warning-state-ok {
	margin-top: 10px;
	margin-bottom: 20px;
}
</style>
