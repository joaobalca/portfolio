<template>
    <!-- Bootstrap Modal -->
    <div class="modal modal-xl fade" :id="UNIQUE_ID" tabindex="-1" :aria-labelledby="`${UNIQUE_ID}-label`" aria-hidden="true">
        <div class="modal-dialog modal-dialog-centered">
            <!-- Modal Content -->
            <div class="modal-content">
                <!-- Close Button -->
                <button class="close-button" data-bs-dismiss="modal" aria-label="Close">
                    <i class="fa fa-close"></i>
                </button>

                <!-- Body -->
                <div v-if="props.project" class="modal-body py-5 py-lg-4">
                    <!-- Content Row -->
                    <div class="row text-center text-lg-start p-2 p-md-4">
                        <!-- Image Column -->
                        <div class="col-12 col-lg-4 pe-0 pe-lg-4">
                            <ImageView  :src="props.project['logoUrl']"
                                        :alt="props.project['title']"
                                        :spinner-enabled="true"
                                        class="img-fluid logo my-auto my-lg-0 mb-3"/>
                        </div>

                        <!-- Texts Column -->
                        <div class="col-12 col-lg-8">
                            <!-- Title -->
                            <h1 class="fw-bold mb-3" v-html="props.project['title']"/>

                            <!-- Tags -->
                            <div class="text-4">
                                <i class="fa fa-tag me-1"/>
                                {{ data.getString('tags') }}:
                                <Tags :items="props.project['locales']['tags']" :item-class="'bg-primary'" class="d-inline ms-2"/>
                            </div>

                            <!-- About SubSection -->
                            <div class="modal-subsection">
                                <!-- Title -->
                                <h5 class="d-none d-lg-inline-block fw-bold">
                                    <i class="fa fa-file me-2"/>
                                    <span class="ms-1">{{ data.getString('aboutProject') }}</span>
                                </h5>

                                <!-- Content -->
                                <p v-html="props.project['locales']['description']" class="text-3 text-light-7"/>
                            </div>

                            <!-- Links SubSection -->
                            <div v-if="props.project['links']" class="modal-subsection">
                                <!-- Title -->
                                <h5 class="d-inline-block fw-bold">
                                    <i class="fa fa-external-link me-2" />
                                    <span class="ms-1">{{ data.getString('whereToFind') }}</span>
                                </h5>

                                <!-- Content -->
                                <p v-html="data.getString('whereToFindDescription').replace('${project}', `<strong>${props.project['title']}</strong>`)"
                                   class="text-3 text-light-7"/>

                                <!-- Social Links -->
                                <SocialLinks :items="props.project['links']"/>
                            </div>
                        </div>
                    </div>

                    <!-- Screenshots gallery (optional) -->
                    <div v-if="_images.length"
                         class="modal-subsection screenshots-section px-2 px-md-4">
                        <h5 class="fw-bold text-center text-lg-start">
                            <i class="fa fa-images me-2"/>
                            <span class="ms-1">{{ data.getString('screenshots') }}</span>
                        </h5>

                        <div :id="CAROUSEL_ID"
                             class="carousel slide screenshots-carousel"
                             data-bs-ride="false">
                            <div class="carousel-inner">
                                <div v-for="(image, index) in _images"
                                     :key="image"
                                     class="carousel-item"
                                     :class="{ active: index === activeImageIndex }">
                                    <ImageView :src="image"
                                               :alt="`${props.project['title']} screenshot ${index + 1}`"
                                               :spinner-enabled="true"
                                               class="img-fluid screenshots-slide"/>
                                </div>
                            </div>

                            <button v-if="_images.length > 1"
                                    class="carousel-control-prev"
                                    type="button"
                                    :data-bs-target="`#${CAROUSEL_ID}`"
                                    data-bs-slide="prev">
                                <span class="carousel-control-prev-icon" aria-hidden="true"></span>
                                <span class="visually-hidden">Previous</span>
                            </button>
                            <button v-if="_images.length > 1"
                                    class="carousel-control-next"
                                    type="button"
                                    :data-bs-target="`#${CAROUSEL_ID}`"
                                    data-bs-slide="next">
                                <span class="carousel-control-next-icon" aria-hidden="true"></span>
                                <span class="visually-hidden">Next</span>
                            </button>
                        </div>

                        <div v-if="_images.length > 1" class="screenshots-thumbs">
                            <button v-for="(image, index) in _images"
                                    :key="`thumb-${image}`"
                                    type="button"
                                    class="screenshots-thumb"
                                    :class="{ active: index === activeImageIndex }"
                                    @click="_selectImage(index)">
                                <ImageView :src="image"
                                           :alt="`${props.project['title']} thumbnail ${index + 1}`"
                                           :spinner-enabled="false"
                                           class="img-fluid"/>
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script setup>
import {computed, nextTick, onMounted, ref, watch} from "vue"
import {useData} from "../../../composables/data.js"
import Modal from '/node_modules/bootstrap/js/src/modal'
import Carousel from '/node_modules/bootstrap/js/src/carousel'
import ImageView from "../../widgets/ImageView.vue"
import Tags from "../../widgets/Tags.vue"
import SocialLinks from "../../widgets/SocialLinks.vue"

/**
 * @property {Object} project
 */
const props = defineProps({
    project: Object
})

const data = useData()
const activeImageIndex = ref(0)

/**
 * @const
 * @type {string}
 */
const UNIQUE_ID = 'gallery-modal'

/**
 * @const
 * @type {string}
 */
const CAROUSEL_ID = 'gallery-modal-carousel'

/**
 * @type {Modal|null}
 */
let bsModal = null

/**
 * @type {Carousel|null}
 */
let bsCarousel = null

/**
 * Optional screenshot list on a project item.
 * @type {import('vue').ComputedRef<string[]>}
 */
const _images = computed(() => {
    const images = props.project?.images
    return Array.isArray(images) ? images.filter(Boolean) : []
})

/**
 * @public
 */
const display = () => {
    if(!bsModal) {
        return
    }

    activeImageIndex.value = 0
    bsModal.show()
    nextTick(() => {
        _ensureCarousel()
        if (bsCarousel) {
            bsCarousel.to(0)
        }
    })
}

/**
 * @param {Number} index
 * @private
 */
const _selectImage = (index) => {
    activeImageIndex.value = index
    _ensureCarousel()
    if (bsCarousel) {
        bsCarousel.to(index)
    }
}

/**
 * @private
 */
const _ensureCarousel = () => {
    const el = document.getElementById(CAROUSEL_ID)
    if (!el || !_images.value.length) {
        bsCarousel = null
        return
    }

    bsCarousel = Carousel.getOrCreateInstance(el, {
        interval: false,
        wrap: true,
        keyboard: true
    })
}

/**
 * @private
 */
const _onSlide = (event) => {
    if (typeof event?.to === 'number') {
        activeImageIndex.value = event.to
    }
}

/**
 * @private
 */
onMounted(() => {
    const elModal = document.getElementById(UNIQUE_ID)
    if(elModal) {
        bsModal = new Modal(elModal, {})
    }

    const elCarousel = document.getElementById(CAROUSEL_ID)
    if (elCarousel) {
        elCarousel.addEventListener('slid.bs.carousel', _onSlide)
    }
})

watch(_images, () => {
    nextTick(() => _ensureCarousel())
})

defineExpose({
    display
})
</script>

<style lang="scss" scoped>
@import "/src/scss/_theming.scss";

.modal-content {
    background-color: $light-1;
}

.close-button {
    position: absolute;
    right: 20px;
    top: 10px;

    z-index: 99;
    padding: 0;
    margin: 0;
    font-size: 1.7rem;

    background-color: transparent;
    border-color: transparent;
    color:$light-4;

    &:hover {
        color: $primary;
    }
}

.modal-subsection {
    @include generate-dynamic-styles-with-hash((
        xxxl: (margin-top: 35px),
        lg:   (margin-top: 30px),
        sm:   (margin-top: 25px)
    ))
}

.logo {
    @include generate-dynamic-styles-with-hash((
        xxxl: (max-width: 100%),
        lg:   (max-width: max(150px, min(20vw, 100%)), margin-bottom: 80px),
        sm:   (max-width: max(130px, 30vw))
    ));

    margin: auto;
    width: 100%;
    aspect-ratio: 1/1;
    border-radius: 27.5%;
}

.screenshots-section {
    text-align: left;
}

.screenshots-carousel {
    margin-top: 1rem;
    border-radius: 0.75rem;
    overflow: hidden;
    background: $light-2;
    border: 1px solid $light-3;
}

.screenshots-slide {
    display: block;
    width: 100%;
    max-height: min(48vh, 420px);
    object-fit: contain;
    background: $light-2;
}

:deep(.screenshots-slide img),
:deep(.screenshots-slide .image-view),
:deep(.screenshots-slide) {
    width: 100%;
}

:deep(.screenshots-slide img) {
    max-height: min(48vh, 420px);
    object-fit: contain;
    width: 100%;
}

.carousel-control-prev,
.carousel-control-next {
    width: 3rem;
    opacity: 0.85;

    &:hover {
        opacity: 1;
    }
}

.screenshots-thumbs {
    display: flex;
    flex-wrap: nowrap;
    gap: 0.55rem;
    overflow-x: auto;
    padding: 0.85rem 0.15rem 0.25rem;
    margin-top: 0.35rem;
    scrollbar-width: thin;
}

.screenshots-thumb {
    flex: 0 0 auto;
    width: 104px;
    height: 68px;
    padding: 0;
    border: 2px solid transparent;
    border-radius: 0.45rem;
    overflow: hidden;
    background: $light-2;
    cursor: pointer;

    &:hover {
        border-color: fade-out($primary, 0.45);
    }

    &.active {
        border-color: $primary;
    }

    :deep(img) {
        width: 100%;
        height: 100%;
        object-fit: cover;
        display: block;
    }
}
</style>
