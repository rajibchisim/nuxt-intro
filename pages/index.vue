<template>
  <div>
    <MainMenu/>
    <Header/>

    <div class="container mx-auto flex flex-wrap py-6">

        <!-- Posts Section -->
        <section class="w-full md:w-2/3 flex flex-col items-center px-3">

            <article
              class="flex flex-col shadow my-4"
              v-for="post in posts" v-bind:key="post.title"
            >
                <!-- Article Image -->
                <a href="#" class="hover:opacity-75">
                    <img v-bind:src="post.thumbnail">
                </a>
                <div class="bg-white flex flex-col justify-start p-6">
                    <a href="#" class="text-blue-700 text-sm font-bold uppercase pb-4">{{ post.tags }}</a>
                    <a href="#" class="text-3xl font-bold hover:text-gray-700 pb-4">{{ post.title }}</a>
                    <p href="#" class="text-sm pb-3">
                        By <a href="#" class="font-semibold hover:text-gray-800">Author</a>, Published on {{ dateString(post.date)}}
                    </p>
                    <a href="#" class="pb-6">{{ post.description }}</a>
                    <a v-bind:href="'/'+post.slug" class="uppercase text-gray-800 hover:text-black">Continue Reading <i class="fas fa-arrow-right"></i></a>
                </div>
            </article>

            <!-- Pagination -->
            <div class="flex items-center py-8">
                <a v-bind:href="pagination.previous.url"
                  v-bind:disabled="pagination.previous.url"
                  v-bind:class="pagination.previous.url ? 'text-gray-800 hover:text-gray-900' : 'text-gray-300'"
                  class="h-10 w-10 font-semibold text-sm flex items-center justify-center mr-3"
                >Previous <i class="fas fa-arrow-right ml-2"></i></a>
                <a v-bind:href="pageLink.url"
                  class="h-10 w-10 font-semibold text-gray-800 hover:bg-blue-600 hover:text-white text-sm flex items-center justify-center"
                  v-bind:class="{'bg-blue-800': pageLink.page == pagination.current }"
                  v-for="(pageLink, index) in pagination.pages" v-bind:key="index"
                >{{ pageLink.page }}</a>
                <a v-bind:href="pagination.next.url"
                  v-bind:disabled="pagination.next.url"
                  v-bind:class="pagination.next.url ? 'text-gray-800 hover:text-gray-900' : 'text-gray-300'"
                  class="h-10 w-10 font-semibold text-sm flex items-center justify-center ml-3"
                >Next <i class="fas fa-arrow-right ml-2"></i></a>
            </div>

        </section>

        <!-- Sidebar Section -->
        <aside class="w-full md:w-1/3 flex flex-col items-center px-3">

            <div class="w-full bg-white shadow flex flex-col my-4 p-6">
                <p class="text-xl font-semibold pb-5">About Us</p>
                <p class="pb-2">Lorem ipsum dolor sit amet, consectetur adipiscing elit. Maecenas mattis est eu odio sagittis tristique. Vestibulum ut finibus leo. In hac habitasse platea dictumst.</p>
                <a href="#" class="w-full bg-blue-800 text-white font-bold text-sm uppercase rounded hover:bg-blue-700 flex items-center justify-center px-2 py-3 mt-4">
                    Get to know us
                </a>
            </div>

            <div class="w-full bg-white shadow flex flex-col my-4 p-6">
                <p class="text-xl font-semibold pb-5">Instagram</p>
                <div class="grid grid-cols-3 gap-3">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=1">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=2">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=3">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=4">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=5">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=6">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=7">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=8">
                    <img class="hover:opacity-75" src="https://source.unsplash.com/collection/1346951/150x150?sig=9">
                </div>
                <a href="#" class="w-full bg-blue-800 text-white font-bold text-sm uppercase rounded hover:bg-blue-700 flex items-center justify-center px-2 py-3 mt-6">
                    <i class="fab fa-instagram mr-2"></i> Follow Us
                </a>
            </div>

        </aside>

    </div>


  <Footer/>
  </div>
</template>

<script>
export default {
  async asyncData({$content, query}){
    const collection = await $content('blog').only(['title']).fetch()
    let currentPage = parseInt(query.page == undefined ? 1 : query.page)
    console.log('currentpage: '+currentPage)
    let totalPosts = collection.length
    let perPage = 5
    let lastPage = Math.ceil(totalPosts/perPage)
    let offset = 0

    if(currentPage == 1) {
      offset = 0
    } else if(currentPage == lastPage) {
      offset = totalPosts - (totalPosts%perPage)
    } else if(currentPage > 1) {
      offset = (currentPage - 1) * perPage
    }

    const posts = await $content('blog')
                          .limit(perPage)
                          .skip(offset)
                          .fetch()

    const previous = { url: currentPage <= 1 ? null : '/?page='+(currentPage - 1)}
    const next = { url: lastPage <= currentPage ? null : '/?page='+(currentPage + 1) }
    const pages = []
    for(let i = 1; i <= lastPage; i++){
      if(i == 1) {
        pages.push({url: '/', page: i})
      } else {
        pages.push({url: '/?page='+i, page: i})
      }
    }
    const pagination = {
      previous,
      next,
      pages,
      current: currentPage
    }
    return {
      posts,
      pagination
    }
  },
  methods: {
    dateString(stringDate){
      let date = new Date(stringDate)
      let months = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December']
      return `${date.getDate()} ${months[date.getMonth()]} ${date.getFullYear()}`
    }
  }
}
</script>
