<template>
    <div class="info-tags">
        <span v-for="(item, index) in props.items"
              :key="index"
              class="info-tag text-2">
            {{ _labelFor(item) }}
        </span>
    </div>
</template>

<script setup>
/**
 * Chip cloud layout for skill-style tag groups.
 * @property {Object[]|String[]} items
 */
const props = defineProps({
    items: Array
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

.info-tags {
    display: flex;
    flex-wrap: wrap;
    gap: 0.55rem 0.55rem;
}

.info-tag {
    display: inline-flex;
    align-items: center;
    max-width: 100%;

    padding: 0.45rem 0.85rem;
    border-radius: 999px;
    border: 1px solid rgba($dark, 0.08);

    background-color: $light-1;
    color: $light-7;
    font-weight: 600;
    line-height: 1.25;
    word-break: break-word;

    transition: background-color 0.2s ease, border-color 0.2s ease, color 0.2s ease, transform 0.2s ease;

    &:hover {
        background-color: fade-out($primary, 0.9);
        border-color: fade-out($primary, 0.55);
        color: darken($primary, 8%);
        transform: translateY(-1px);
    }

    @include media-breakpoint-down(md) {
        padding: 0.4rem 0.75rem;
        font-size: 0.92em;
    }

    @include media-breakpoint-down(sm) {
        padding: 0.35rem 0.7rem;
    }
}

@media (prefers-color-scheme: dark) {
    .info-tag {
        background-color: lighten($dark, 8%);
        border-color: rgba($light, 0.12);
        color: $light-2;

        &:hover {
            background-color: fade-out($primary, 0.78);
            border-color: fade-out($primary, 0.35);
            color: lighten($primary, 18%);
        }
    }
}
</style>
