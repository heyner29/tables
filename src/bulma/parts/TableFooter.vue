<template>
    <tfoot>
        <tr>
            <td v-if="state.template.selectable"/>
            <td v-if="hiddenColumns().length || state.template.preview">
                <span class="icon is-small hidden-control"
                    :aria-visible="isExpanded"
                    @click="isExpanded = !isExpanded"
                    v-if="hiddenTotals.length">
                    <fa icon="chevron-right"/>
                </span>
            </td>
            <td v-if="state.template.crtNo"/>
            <td class="has-text-centered is-bold"
                v-if="visibleColumn(state.template.columns[0])">
                {{ i18n("Total") }}
            </td>
            <template v-for="i in columns.length - 1">
                <td class="is-bold"
                    :class="[{ 'is-money' : columns[i].money }, columnAlignment(columns[i])]"
                    :key="i"
                    v-if="visibleColumn(columns[i])">
                    <span v-if="
                        columns[i].meta.total || columns[i].meta.rawTotal
                        || columns[i].meta.average
                    ">{{
                        columns[i].money
                            ? state.body.total[columns[i].name]
                            : totalFormat(state.body.total[columns[i].name])
                    }}</span>
                    <slot :name="`${columns[i].name}_custom_total`"
                        :total="state.body.total"
                        v-else-if="columns[i].meta.customTotal"/>
                </td>
            </template>
            <td v-if="state.template.actions"/>
        </tr>
        <tr v-if="isExpanded">
            <td class="has-text-right"
                :colspan="hiddenColspan()">
                <ul>
                    <li v-for="cell in hiddenTotals"
                        class="is-bold"
                        :class="[{ 'is-money' : cell.money }, columnAlignment(cell)]"
                        :key="cell.name">
                        <span>{{ i18n(cell.label) }}:</span>
                        <span v-if="cell.meta.total || cell.meta.rawTotal ">{{
                            cell.money
                                ? state.body.total[cell.name]
                                : totalFormat(state.body.total[cell.name])
                        }}</span>
                        <slot :name="`${cell.name}_custom_total`"
                            :total="state.body.total"
                            v-else-if="cell.meta.customTotal"/>
                    </li>
                </ul>
            </td>
        </tr>
    </tfoot>
</template>

<script>
import { library } from '@fortawesome/fontawesome-svg-core';
import { faChevronRight } from '@fortawesome/free-solid-svg-icons';

library.add(faChevronRight);

export default {
    name: 'TableFooter',

    inject: [
        'hiddenColspan', 'hiddenColumns', 'i18n', 'totalFormat',
        'visibleColumn', 'visibleColumns', 'columnAlignment', 'state',
    ],

    data: () => ({
        isExpanded: false,
    }),

    computed: {
        columns() {
            return this.visibleColumns();
        },
        hiddenTotals() {
            return this.hiddenColumns().filter(({ meta }) => meta.total
                || meta.rawTotal || meta.customTotal);
        },
        hiddenTotalsCount() {
            return this.hiddenTotals.length;
        },
    },

    watch: {
        hiddenTotalsCount(count) {
            if (!count) {
                this.isExpanded = false;
            }
        },
    },
};
</script>
