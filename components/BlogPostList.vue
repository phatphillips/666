<script setup lang="ts">
const props = defineProps({
  list: {
    type: Array,
    default: () => [],
  },
})

const displayRange = ref({
  end: 4,
})

const formattedEventDate = (date: string) => {
  return new Date(date).toLocaleString('en-US', {
    month: 'long',
    day: 'numeric',
    year: 'numeric',
  })
}

const selectedTag = ref('')

const filteredList = computed(() => {
  if (props.list && props.list.length > 0) {
    return props.list
      .filter((item) => {
        const isBlogPost = item._path.includes('/blog/')
        const isReadyToPublish = new Date(item.date) <= new Date()
        const hasTags = item.tags && item.tags.includes(selectedTag.value)

        const shouldPublish = selectedTag.value
          ? isBlogPost && isReadyToPublish && hasTags
          : isBlogPost && isReadyToPublish

        return shouldPublish
      })
      .sort((a, b) => new Date(b.date) - new Date(a.date))
  }

  return false
})

function loadMore() {
  displayRange.value.end += 5
}

function updateSelectedTag(tag) {
  selectedTag.value = tag
}
</script>

<template>
  <div class="blog-list__container">
    <div class="blog-list__header">
      <h1 class="blog-list-title">Blog Posts</h1>
      <div style="margin-left: 20px" class="tooltip-ex">
        <strong><i class="fas fa-info-circle"></i></strong>
        <span class="tooltip-ex-text tooltip-ex-bottom"
          >Everything here is written via the stream of consciousness writing
          technique and should be treated as a rough draft at best (i.e., very
          little editing). However, I promise you one thing:
          <strong
            ><em
              >none of the posts below are meant to be offensive or malicious in
              any way</em
            ></strong
          >. So if you read something that you feel is offensive, please let me
          know and I'll be happy to take the time to rewrite it!</span
        >
      </div>
    </div>
    <p>
      To subscribe via RSS, you can use
      <a href="/rss.xml">https://www.bencodezen.io/rss.xml</a>.
    </p>

    <h2 class="blog-list-subtitle">Most Recent</h2>

    <div v-if="selectedTag" class="filtered-heading">
      <h2>Filtered by {{ selectedTag }} tag</h2>
      <button
        type="button"
        class="btn clear-filter-btn"
        @click="selectedTag = ''"
      >
        Clear filter
      </button>
    </div>
    <ul class="blog-list">
      <li
        v-for="(item, index) in filteredList"
        :key="`blog-post-${index}`"
        class="blog-list__item"
      >
        <BlogPostPreview
          v-show="index <= displayRange.end"
          :excerpt="item.excerpt"
          :path="item._path"
          :published="item.date"
          :tags="item.tags"
          :title="item.title"
          @updateSelectedTag="updateSelectedTag"
        />
      </li>
    </ul>

    <div
      v-if="displayRange.end <= filteredList.length"
      class="pagination-section"
    >
      <button class="button--load-more" type="button" @click="loadMore">
        Load More
      </button>
    </div>
  </div>
</template>

<style lang="scss" scoped>
$primary-color: #22aaff;

.blog-list {
  padding: 0;
  margin: 0;
}

.blog-list-subtitle {
  @apply text-3xl;
  @apply font-bold;
  font-family: $ff-sans;
  border-bottom: 1px solid $c-border;
  @apply mb-3;
  @apply pb-1;
}

.blog-list-title {
  @apply text-4xl;
  @apply font-bold;
  font-family: $ff-sans;
  @apply mb-4;
}

.blog-list__container {
  margin-top: 2rem;
}

.blog-list__header {
  display: flex;
  align-items: center;
}

.blog-list__item {
  list-style-type: none;
}

.button--load-more {
  background-color: $primary-color;
  border-radius: 4px;
  border-color: $primary-color;
  color: #fff;
  font-size: 1rem;
  padding: 0.5rem 0.75rem;
  text-transform: uppercase;
  font-weight: 500;
  box-shadow: 0 0;
  cursor: pointer;
  transition: background-color 0.2s ease-in, color 0.2s ease-in;
}

.button--load-more:hover {
  background-color: #fff;
  border: 1px solid $primary-color;
  border-radius: 4px;
  color: $primary-color;
}

.clear-filter-btn {
  align-self: center;
  margin-left: 20px;
}

.filtered-heading {
  display: flex;
}

.pagination {
  text-align: center;
}

.tooltip-ex {
  /* Container for our tooltip */
  position: relative;
  display: inline-block;
  cursor: help;
  margin-right: 20px;
  display: inline-block;
  float: left;
}

.tooltip-ex-bottom {
  top: 135%;
  left: 50%;
  margin-left: -60px;
}

.tooltip-ex-text {
  visibility: hidden;
  position: absolute;
  width: 300px;
  background-color: #555;
  color: #fff;
  text-align: center;
  padding: 20px;
  border-radius: 6px;
  z-index: 1;
  opacity: 0;
  transition: opacity 0.6s;
}

.tooltip-ex:hover .tooltip-ex-text {
  /* Makes tooltip visible when hovered on */
  visibility: visible;
  opacity: 1;
}
</style>
