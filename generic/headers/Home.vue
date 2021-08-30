<template>
  <div>
    <div
      v-whppt-image="currentImage"
      data-sizes='{ "desktop": { "width": 1920, "height": 1080, "quality": 6, "aspectRatio":1.77778 }, "mobile": { "width": 750, "height": 1000, "quality": 6, "aspectRatio":0.75 } }'
      class="relative bg-gray-600 h-80vh"
    >
      <div class="absolute inset-0 w-full h-full z-0 block">
        <div class="w-full h-full">
          <client-only>
            <progressive-background
              :src="$whppt.getImage(currentImage.image.imageId, 1920, 1080, currentImage.image.desktop)"
              :placeholder="$whppt.getImage(currentImage.image.imageId, 192, 108, currentImage.image.desktop)"
              no-ratio
              class="bg-cover bg-no-repeat bg-center hidden sm:block"
            />
            <progressive-background
              :src="$whppt.getImage(currentImage.image.imageId, 750, 1000, currentImage.image.mobile)"
              :placeholder="$whppt.getImage(currentImage.image.imageId, 75, 100, currentImage.image.mobile)"
              no-ratio
              class="bg-cover bg-no-repeat bg-center sm:hidden"
            />
          </client-only>
        </div>
      </div>
      <!-- <scrim gradient="linear-gradient(to bottom, rgba(0, 37, 67, 0.6), rgba(0, 37, 67, 0.6) 100%);" /> -->
      <div class="absolute inset-0 flex flex-col justify-center container">
        <div class="flex">
          <div class="w-1/12" />
          <div class="w-10/12">
            <div
              v-whppt-formatted-text="header"
              data-property="title"
              class="hidden lg:inline-block text-3.5xl text-white header-title font-light richText"
              style="line-height: 0.9"
              v-html="header.title || 'Header Title'"
            ></div>
            <div
              class="lg:hidden text-3xl text-white header-title font-light richText"
              style="line-height: 0.9"
              v-html="header.mobileTitle || header.title || 'Header Title'"
            ></div>
            <whppt-link
              v-if="header.mobileLink.href || header.mobileLink.to"
              :to="header.mobileLink"
              class="
                lg:hidden
                font-bold
                py-4
                px-8
                mt-8
                rounded
                inline-flex
                justify-center
                items-center
                transition
                ease-in-out
                duration-300
                text-white
              "
              :style="`background-color: ${theme.primary}`"
            >
              {{ header.mobileLink.text }}
            </whppt-link>
            <div v-if="inEditor" class="text-white mt-8 text-lg">
              <div v-whppt-formatted-text="header" data-property="mobileTitle" class="mb-2">Manage Mobile Title</div>
              <div v-whppt-link="header.mobileLink" class="mb-2">Manage Mobile CTA</div>
              <div v-whppt-text="header" data-property="videoId" class="mb-4">Manage Video</div>
              <div v-whppt-list="{ data: header, addNew }" data-property="images" class="mb-4">Manage Images</div>
              <div class="flex items-center mb-4">
                <div>Select an Image -&nbsp;</div>
                <button
                  v-for="(image, index) in header.images"
                  :key="`header-image-${index}`"
                  class="p-1"
                  :class="{ 'font-bold': index === currentImageIndex }"
                  @click.stop="manualSelectImage(index)"
                >
                  {{ index + 1 }}
                </button>
              </div>
              <!-- <div class="flex items-center">
                <button class="italic" @click.stop="manualPrevImage">Previous Image</button>
                <button class="ml-4 italic" @click.stop="manualNextImage">Next Image</button>
              </div> -->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { forEach } from 'lodash';
import { mapActions, mapGetters } from 'vuex';

export default {
  name: 'HomeHeader',
  props: {
    header: { type: Object, default: () => ({ images: [] }) },
  },
  data: () => ({ currentImageIndex: 0, timer: undefined, innerWidth: 1025 }),
  computed: {
    ...mapGetters(['inEditor']),
    currentImage() {
      return this.header.images[this.currentImageIndex] || {};
    },
  },
  watch: {
    'header.images'() {
      this.currentImageIndex = 0;
    },
  },
  mounted() {
    if (process.browser) {
      this.innerWidth = window.innerWidth;
    }
    this.preloadImages();
    this.timer = setInterval(() => {
      this.nextImage();
    }, 3000);
  },
  destroyed() {
    clearInterval(this.timer);
  },
  methods: {
    ...mapActions('whppt/editor', ['pushSelectedComponentState']),

    addNew() {
      this.pushSelectedComponentState({
        path: 'images',
        value: { image: { imageId: '' } },
      });
    },
    nextImage() {
      if (this.inEditor) return;
      this.currentImageIndex = (this.currentImageIndex + 1) % this.header.images.length;
    },
    manualNextImage() {
      this.currentImageIndex = (this.currentImageIndex + 1) % this.header.images.length;
    },
    manualPrevImage() {
      if (this.currentImageIndex === 0) this.currentImageIndex = this.header.images.length - 1;
      else this.currentImageIndex--;
    },
    manualSelectImage(index) {
      this.currentImageIndex = index;
    },
    preloadImages() {
      const { images } = this.header;

      forEach(images, i => {
        if (i.image && i.image.imageId) {
          const mobileImage = new Image();
          const mobileImagePlaceholder = new Image();
          const desktopImage = new Image();
          const desktopImagePlaceholder = new Image();
          mobileImage.src = this.$whppt.getImage(i.image.imageId, 750, 1000, i.image.mobile);
          mobileImagePlaceholder.src = this.$whppt.getImage(i.image.imageId, 75, 100, i.image.mobile);
          desktopImage.src = this.$whppt.getImage(i.image.imageId, 1920, 1080, i.image.desktop);
          desktopImagePlaceholder.src = this.$whppt.getImage(i.image.imageId, 192, 108, i.image.desktop);
        }
      });
    },
  },
};
</script>

<style lang="scss">
.header-title.richText {
  p {
    line-height: 0.9;
  }
}
</style>
