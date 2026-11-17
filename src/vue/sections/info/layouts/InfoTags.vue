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
    gap: 0.28rem;
}

.info-tags-label {
    margin: 0;
    letter-spacing: 0.06em;
    font-size: 0.68rem;
    font-weight: 500;
    line-height: 1.2;
    color: $light-6 !important;
}

.info-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.3rem;
    align-items: center;
}

.info-tag {
    display: inline-flex;
    align-items: center;
    max-width: 100%;

    padding: 0.18rem 0.5rem;
    border-radius: 999px;
    border: 1px solid rgba($dark, 0.06);

    background-color: rgba($dark, 0.03);
    color: $light-7;
    font-size: 0.76rem;
    font-weight: 500;
    line-height: 1.25;
    word-break: break-word;

    transition: background-color 0.15s ease, border-color 0.15s ease, color 0.15s ease;

    &:hover {
        background-color: fade-out($primary, 0.92);
        border-color: fade-out($primary, 0.7);
        color: darken($primary, 6%);
    }

    @include media-breakpoint-down(sm) {
        padding: 0.16rem 0.45rem;
        font-size: 0.72rem;
    }
}

@media (prefers-color-scheme: dark) {
    .info-tags-label {
        color: $light-5 !important;
    }

    .info-tag {
        background-color: rgba($light, 0.05);
        border-color: rgba($light, 0.08);
        color: $light-2;

        &:hover {
            background-color: fade-out($primary, 0.82);
            border-color: fade-out($primary, 0.5);
            color: lighten($primary, 16%);
        }
    }
}
</style>
