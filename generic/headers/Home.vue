<template>
  <div>
    <div
      v-whppt-image="header.image"
      data-sizes='{ "desktop": { "width": 1920, "height": 1080, "quality": 6, "aspectRatio":1.77778 }, "mobile": { "width": 750, "height": 1000, "quality": 6, "aspectRatio":0.75 } }'
      class="relative bg-gray-600 h-80vh"
    >
      <!-- <div class="absolute inset-0 w-full h-full z-0 block">
        <div v-if="header && header.image && header.image.href" class="w-full h-full">
          <client-only>
            <progressive-background
              :src="$whppt.getImage(header.image.imageId, 1920, 1080, header.image.desktop)"
              :placeholder="$whppt.getImage(header.image.imageId, 192, 108, header.image.desktop)"
              no-ratio
              class="bg-cover bg-no-repeat bg-center hidden sm:block"
            />
            <progressive-background
              :src="$whppt.getImage(header.image.imageId, 750, 1000, header.image.mobile)"
              :placeholder="$whppt.getImage(header.image.imageId, 75, 100, header.image.mobile)"
              no-ratio
              class="bg-cover bg-no-repeat bg-center sm:hidden"
            />
          </client-only>
        </div>
      </div> -->
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
            <!-- <whppt-link
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
            </whppt-link> -->
            <div v-if="inEditor" class="text-white mt-8 text-lg">
              <div v-whppt-formatted-text="header" data-property="mobileTitle" class="mb-2">Manage Mobile Title</div>
              <!-- <div v-whppt-link="header.mobileLink" class="mb-2">Manage Mobile CTA</div> -->
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from 'vuex';

export default {
  name: 'HomeHeader',
  props: {
    header: { type: Object, default: () => ({}) },
  },
  data: () => ({ innerWidth: 1025 }),
  computed: {
    ...mapGetters(['inEditor']),
    // currentImage() {
    //   return this.header.image || { imageId: '' };
    // },
  },
  mounted() {
    if (process.browser) {
      this.innerWidth = window.innerWidth;
    }
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
