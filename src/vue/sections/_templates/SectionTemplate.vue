<template>
    <!-- Website Section -->
    <section v-show="isVisible" :id="props.sectionData['id']" :class="classList">
        <!-- Section Container -->
        <div class="section-container" v-if="props.sectionData.content">
            <!-- Section Header -->
            <div class="section-header" v-if="props.sectionData.content['locales']['title']">
                <!-- Title -->
                <h1 class="section-title mb-1 mb-lg-2 fw-bold text-uppercase" v-html="sectionTitle"/>

                <!-- Divider -->
                <hr class="solid-divider ms-1 me-1 mb-3 mb-lg-4">

                <!-- Description -->
                <p v-if="props.sectionData.content['locales']['description']" class="lead text-muted">
                    {{props.sectionData.content['locales']['description']}}
                </p>
            </div>

            <!-- Section Content -->
            <div class="section-content">
                <slot/>
            </div>
        </div>
    </section>
</template>

<script setup>
import {computed} from "vue"
import {useNavigation} from "../../../composables/navigation.js"
import {useData} from "../../../composables/data.js"

/**
 * @property {Object} sectionData
 */
const props = defineProps({
    sectionData: Object
})

const data = useData()
const navigation = useNavigation()

/**
 * @type {ComputedRef<Boolean>}
 */
const isVisible = computed(() => {
    return props.sectionData && navigation.isSectionVisible(props.sectionData['id'])
})

/**
 * @type {ComputedRef<string>}
 */
const classList = computed(() => {
    let classList = 'section'

    if(navigation.isAllAtOnceMode()) {
        classList += ' section-with-division'
    }

    if(props.sectionData['cover']) {
        classList += ' section-cover'
    }

    if(props.sectionData['compact']) {
        classList += ' section-compact'
    }

    return classList
})

/**
 * @type {ComputedRef<String>}
 */
const sectionTitle = computed(() => {
    if(navigation.isAllAtOnceMode()) {
        return props.sectionData.content['locales']['title']
    }
    else {
        return data.getString(props.sectionData['id'])
    }
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.section {
    position: relative;
    display: flex;
    min-height: 100vh;

    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        min-height: calc(100vh - 170px);
    }

    &-with-division {
        border-bottom: 1px solid $dark;
    }

    &-cover {
        @include media-breakpoint-up($navigation-sidebar-breakpoint) {
            .section-container {
                margin: auto 0;
            }
        }
    }

    /*
     * Compact sections (e.g. Skills) skip the full-viewport min-height and
     * use tighter vertical padding so short content does not leave large gaps.
     */
    &-compact {
        min-height: 0;

        @include media-breakpoint-down($navigation-sidebar-breakpoint) {
            min-height: 0;
        }

        .section-container {
            @include generate-dynamic-styles-with-hash((
                xxxl: (padding: 2.75rem clamp(5rem, 5vw, 10rem) 3rem),
                xxl:  (padding: 2.25rem 4rem 2.5rem),
                lg:   (padding: 1.75rem 2rem 2rem),
                md:   (padding: 1.35rem 1.5rem 1.6rem),
                sm:   (padding: 1.25rem 1.2rem 1.75rem)
            ));
        }

        .section-header {
            @include generate-dynamic-styles-with-hash((
                xxxl: (margin-bottom: 1.5rem),
                xxl:  (margin-bottom: 1.25rem),
                xl:   (margin-bottom: 1.1rem),
                lg:   (margin-bottom: 1rem),
            ));
        }
    }
}

.section-container {
    @include generate-dynamic-styles-with-hash((
        xxxl:   (padding: clamp(5rem, 5vw, 10rem) clamp(5rem, 5vw, 10rem) clamp(6rem, 5vw, 10rem)),
        xxl:    (padding: 4rem 4rem 6rem),
        lg:     (padding: 2rem 2rem),
        md:     (padding: 1.5rem 1.5rem),
        sm:     (padding: 1.5rem 1.2rem 3rem)
    ));

    width: min(100%, $max-content-width);

    @include media-breakpoint-down($navigation-sidebar-breakpoint) {
        margin-bottom: $nav-tabs-height;
    }
}

.section-header {
    @include generate-dynamic-styles-with-hash((
        xxxl: (margin-bottom:3rem),
        xxl: (margin-bottom:2.5rem),
        xl: (margin-bottom:2rem),
        lg: (margin-bottom:1.5rem),
    ))
}
</style>