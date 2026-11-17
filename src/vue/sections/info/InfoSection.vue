<template>
    <SectionTemplate :section-data="props.sectionData">
        <div class="info-row row gx-4 gx-xl-5">
            <div v-for="subcategory in props.sectionData['content']['subcategories']"
                 class="col-12 subcategory-col"
                 :class="{'subcategory-col-tags': subcategory['type'] === 'tags'}">
                <!-- Subcategory Title (non-tag layouts keep the standard heading) -->
                <SubHeading v-if="subcategory['locales']['title'] && subcategory['type'] !== 'tags'"
                            :title="subcategory['locales']['title']"
                            :fa-icon="subcategory['faIcon']"/>

                <!-- Group Component -->
                <component :is="_getComponentFor(subcategory)"
                           :items="_fetchAndParseItemsFor(subcategory)"
                           :title="subcategory['type'] === 'tags' ? subcategory['locales']['title'] : undefined"/>
            </div>
        </div>
    </SectionTemplate>
</template>

<script setup>
import SectionTemplate from "../_templates/SectionTemplate.vue"
import SubHeading from "../_templates/SubHeading.vue"

import InfoList from "./layouts/InfoList.vue"
import InfoGrid from "./layouts/InfoGrid.vue"
import InfoPie from "./layouts/InfoPie.vue"
import InfoTags from "./layouts/InfoTags.vue"

import {useLayout} from "../../../composables/layout.js"
import {useUtils} from "../../../composables/utils.js"
import {useData} from "../../../composables/data.js"

/**
 * @property {Object} sectionData
 */
const props = defineProps({
    sectionData: Object
})

const data = useData()
const layout = useLayout()
const utils = useUtils()

/**
 * @const
 */
const COMPONENTS_MAP = {
    grid: InfoGrid,
    pie: InfoPie,
    list: InfoList,
    tags: InfoTags,
    fallback: InfoList
}

/**
 * @const
 * @type {Object}
 */
const STYLE_PREFERENCES = layout.getStylePreferencesForPlugins()

/**
 * @param {Object} subcategory
 * @private
 */
const _getComponentFor = (subcategory) => {
    const dataType = subcategory['type']
    return COMPONENTS_MAP[dataType] || COMPONENTS_MAP['fallback']
}

/**
 * @param subcategory
 * @return {Object[]}
 * @private
 */
const _fetchAndParseItemsFor = (subcategory) => {
    const items = props.sectionData['content']['items'][subcategory['id']] || []

    if(subcategory['type'] === 'tags') {
        return items
    }

    let colorIdCount = 0
    for(const item of items) {
        item.customColor = STYLE_PREFERENCES.colors.random[colorIdCount]
        colorIdCount++

        if(subcategory['progress']) {
            const percentage = item['value']
            const breakpoints = subcategory['progress']
            const parsedPercentage = utils.parsePercentage(percentage, breakpoints)

            item['formattedPercentage'] = typeof breakpoints !== 'string'
                ? data.getString(parsedPercentage)
                : parsedPercentage
        }
    }

    return items
}
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.subcategory-col {
    &:not(:last-child) {
        @include generate-dynamic-styles-with-hash((
            xxxl: (margin-bottom: 2.75rem),
            xl:   (margin-bottom: 1.5rem)
        ));
    }
}

.subcategory-col-tags {
    &:not(:last-child) {
        margin-bottom: 0.7rem !important;

        @include media-breakpoint-down(md) {
            margin-bottom: 0.6rem !important;
        }
    }
}

.info-row {
    overflow-x:hidden;
}
</style>