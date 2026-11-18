<template>
    <div class="info-tags-group">
        <p v-if="props.title" class="info-tags-label text-uppercase">
            {{ props.title }}
        </p>

        <div class="info-tags">
            <span v-for="(item, index) in props.items"
                  :key="index"
                  class="info-tag">
                {{ _labelFor(item) }}
            </span>
        </div>
    </div>
</template>

<script setup>
/**
 * Compact chip cloud for skill-style tag groups.
 * @property {Object[]|String[]} items
 * @property {String} [title]
 */
const props = defineProps({
    items: Array,
    title: String
})

/**
 * @param {Object|String} item
 * @return {String}
 * @private
 */
const _labelFor = (item) => {
    if (typeof item === 'string') {
        return item
    }

    return item?.locales?.title || item?.title || item?.label || ''
}
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.info-tags-group {
    display: flex;
    flex-direction: column;
    gap: 0.45rem;
}

.info-tags-label {
    margin: 0;
    letter-spacing: 0.08em;
    font-size: 0.7rem;
    font-weight: 600;
    line-height: 1.25;
    /* Muted brand accent — distinct from chip text without becoming a heading */
    color: mix($primary, $light-7, 42%) !important;
}

.info-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.3rem;
    align-items: center;
}

/*
 * Skills live in the light content column only. Do not restyle with
 * prefers-color-scheme: dark — that made near-white chip text sit on the
 * always-light page background and washed the chips out.
 */
.info-tag {
    display: inline-flex;
    align-items: center;
    max-width: 100%;

    padding: 0.18rem 0.5rem;
    border-radius: 999px;
    border: 1px solid $light-3;

    background-color: $light-1;
    color: $dark;
    font-size: 0.76rem;
    font-weight: 500;
    line-height: 1.25;
    word-break: break-word;
    opacity: 1;

    transition: background-color 0.15s ease, border-color 0.15s ease, color 0.15s ease;

    &:hover {
        background-color: fade-out($primary, 0.9);
        border-color: fade-out($primary, 0.55);
        color: darken($primary, 8%);
    }

    @include media-breakpoint-down(sm) {
        padding: 0.16rem 0.45rem;
        font-size: 0.72rem;
    }
}
</style>
