<script setup lang="ts">
const route = useRoute()

const { data: localPost } = await useAsyncData('blogPost', () =>
  queryContent(`blog/${route.params.slug}`).findOne()
)

const { data: ghostPost } = useFetch('/api/ghost', {
  method: 'POST',
  body: {
    slug: route.params.slug,
  },
})

const formatPublishDate = (isoDateString) => {
  const dateFormat = new Date(isoDateString)
  const options = {
    year: 'numeric',
    month: 'long',
    day: 'numeric',
  }

  return dateFormat.toLocaleDateString('en-US', options)
}
</script>

<template>
  <article class="post-section">
    <div class="post-content">
      <template v-if="ghostPost">
        <p class="blog-publish-date">
          {{ formatPublishDate(ghostPost.published_at) }}
        </p>

        <h1 class="blog-title">{{ ghostPost.title }}</h1>
        <div v-html="ghostPost.html" />
      </template>
      <template v-else>
        <p class="blog-publish-date">{{ formatPublishDate }}</p>
        <h1 class="blog-title">{{ localPost.title }}</h1>
        <ContentDoc :path="localPost._path" />
      </template>
    </div>
  </article>
</template>

<style lang="scss"></style>
