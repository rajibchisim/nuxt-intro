<template>
  <div>
    <h1>{{ page.title }}</h1>
    <p>{{ page.description }}</p>
    <div>
      <nuxt-content :document="page"></nuxt-content>
    </div>
  </div>
</template>

<script>
export default {
  async asyncData({ $content, params }){
    // const page = //await $content('posts').where({ slug: params.slug }).fetch()
    const page = await $content('blog', params.slug).fetch()
    return {
      page
    }
  }
}
</script>

<style>

</style>
