<!--
  (c) Copyright Ascensio System SIA 2026

  This program is a free software product.
  You can redistribute it and/or modify it under the terms of the GNU Affero General Public License
  (AGPL) version 3 as published by the Free Software Foundation.
  In accordance with Section 7(a) of the GNU AGPL its Section 15 shall be amended to the effect
  that Ascensio System SIA expressly excludes the warranty of non-infringement of any third-party rights.

  This program is distributed WITHOUT ANY WARRANTY;
  without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
  For details, see the GNU AGPL at: http://www.gnu.org/licenses/agpl-3.0.html

  You can contact Ascensio System SIA at 20A-12 Ernesta Birznieka-Upisha street, Riga, Latvia, EU, LV-1050.

  The interactive user interfaces in modified source and object code versions of the Program
  must display Appropriate Legal Notices, as required under Section 5 of the GNU AGPL version 3.

  All the Product's GUI elements, including illustrations and icon sets, as well as technical
  writing content are licensed under the terms of the Creative Commons Attribution-ShareAlike 4.0 International.
  See the License terms at http://creativecommons.org/licenses/by-sa/4.0/legalcode
-->
<template>
	<NcDialog class="eurooffice-download-as-dialog"
		:name="dialogName"
		:buttons="buttons"
		@update:open="$emit('close', false)">
		<div class="eurooffice-download-as">
			<p>{{ promptText }}</p>
			<select v-model="selectedFormat" class="eurooffice-download-as__select">
				<option :value="extension">
					{{ originLabel }}
				</option>
				<option v-for="ext in saveasOptions" :key="ext" :value="ext">
					{{ ext }}
				</option>
			</select>
		</div>
	</NcDialog>
</template>

<script>
import NcDialog from '@nextcloud/vue/components/NcDialog'
import { generateUrl } from '@nextcloud/router'
import { t } from '@nextcloud/l10n'

export default {
	name: 'DownloadAsDialog',

	components: { NcDialog },

	props: {
		fileName: {
			type: String,
			required: true,
		},
		fileId: {
			type: [Number, String],
			required: true,
		},
		extension: {
			type: String,
			required: true,
		},
		saveasOptions: {
			type: Array,
			default: () => [],
		},
	},

	emits: ['close'],

	data() {
		return {
			selectedFormat: this.extension,
			dialogName: t('eurooffice', 'Download as'),
		}
	},

	computed: {
		promptText() {
			return t('eurooffice', 'Choose a format to convert {fileName}', { fileName: this.fileName })
		},
		originLabel() {
			return t('eurooffice', 'Origin format')
		},
		buttons() {
			return [
				{
					label: t('core', 'Cancel'),
					callback: () => this.$emit('close', false),
				},
				{
					label: t('eurooffice', 'Download'),
					variant: 'primary',
					callback: () => this.download(),
				},
			]
		},
	},

	methods: {
		download() {
			const url = generateUrl(
				'apps/eurooffice/downloadas?fileId={fileId}&toExtension={toExtension}',
				{ fileId: this.fileId, toExtension: this.selectedFormat },
			)
			location.href = url
			this.$emit('close', true)
		},
	},
}
</script>

<style scoped lang="scss">
.eurooffice-download-as {
    display: flex;
    flex-direction: column;
    row-gap: 12px;
    padding: 13px;
}

.eurooffice-download-as__select {
    width: 100%;
}

.eurooffice-download-as-dialog :deep(.modal-container) {
    width: 480px;
}
</style>
