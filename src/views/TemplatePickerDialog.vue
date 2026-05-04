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
	<NcDialog class="eurooffice-template-picker-dialog"
		:name="dialogName"
		:buttons="buttons"
		@update:open="$emit('close', false)">
		<ul class="eurooffice-template-container">
			<li v-for="template in items"
				:key="template.id"
				:class="['eurooffice-template-item', { 'eurooffice-template-item--selected': selectedId === template.id }]"
				:tabindex="0"
				@click="selectedId = template.id"
				@keydown.enter.prevent="selectedId = template.id"
				@keydown.space.prevent="selectedId = template.id">
				<img :src="template.icon" :alt="template.name">
				<p>{{ template.name }}</p>
			</li>
		</ul>
	</NcDialog>
</template>

<script>
import NcDialog from '@nextcloud/vue/components/NcDialog'
import { generateUrl } from '@nextcloud/router'
import { t } from '@nextcloud/l10n'

export default {
	name: 'TemplatePickerDialog',

	components: { NcDialog },

	props: {
		fileName: {
			type: String,
			required: true,
		},
		type: {
			type: String,
			required: true,
			validator: v => ['document', 'spreadsheet', 'presentation'].includes(v),
		},
	},

	emits: ['close'],

	data() {
		return {
			selectedId: '0',
			dialogName: t('eurooffice', 'Select template'),
		}
	},

	computed: {
		emptyTemplate() {
			return {
				id: '0',
				name: t('eurooffice', 'Empty'),
				icon: generateUrl('/core/img/filetypes/x-office-{type}.svg', { type: this.type }),
			}
		},
		filteredTemplates() {
			return (OCA.Eurooffice.templates || []).filter(t => t.type === this.type)
		},
		items() {
			return [this.emptyTemplate, ...this.filteredTemplates]
		},
		buttons() {
			return [
				{
					label: t('core', 'Cancel'),
					callback: () => this.$emit('close', false),
				},
				{
					label: t('eurooffice', 'Create'),
					variant: 'primary',
					callback: () => this.create(),
				},
			]
		},
	},

	methods: {
		create() {
			const fileList = OCA.Files.App.fileList
			OCA.Eurooffice.CreateFile(this.fileName, fileList, this.selectedId)
			this.$emit('close', true)
		},
	},
}
</script>

<style scoped lang="scss">
.eurooffice-template-container {
    list-style: none;
    padding: 13px;
    margin: 0;
    display: flex;
    flex-wrap: wrap;
    gap: 12px;
}

.eurooffice-template-item {
    cursor: pointer;
    width: 120px;
    text-align: center;
    padding: 8px;
    border-radius: var(--border-radius);
    border: 2px solid transparent;
    transition: border-color 0.1s ease, background-color 0.1s ease;

    &:hover {
        background: var(--color-background-hover);
    }

    &--selected {
        border-color: var(--color-primary-element);
        background: var(--color-primary-element-light);
    }

    img {
        width: 64px;
        height: 64px;
        display: block;
        margin: 0 auto;
    }

    p {
        margin: 4px 0 0;
        font-size: 0.9em;
        word-break: break-word;
    }
}

.eurooffice-template-picker-dialog :deep(.modal-container) {
    width: min(560px, 90vw);
}
</style>
