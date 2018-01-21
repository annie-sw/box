<template>
	<div id="index">
		Index <br>
		route name: {{ $route.name }}<br>
		route path: {{ $route.path }}<br>
		route params: {{ $route.params[0] }}<br>

		<div class="thumb" v-for="post in posts" v-on:click="selectMedia(post)">
			<img v-bind:src="getThumbnailUrl(post)" />
		</div>
		<div class="clear"/>
	</div>
</template>

<script>
import consts from '@/constants'
import api from '@/api'

export default {
  name: 'index',
  data () {
    return {
      posts: []
    }
  },
  methods: {
    getThumbnailUrl (mediaData) {
      return consts.static_url + mediaData.id
    },
    selectMedia (mediaData) {
      this.$router.push({path: `/media/${mediaData.id}`})
      this.$store.commit('MediaView/setData', mediaData)
    }
  },

  beforeCreate () {
     // URL PATH を処理

    if (this.$route.name === 'index-view') {
      // 表示していた投稿があった
      (async () => {
        this.$store.commit('MediaView/setData', { id: this.$route.params[0] })
      })()
    }

    // 投稿リストを作る
    let query = ''

    if (this.$route.name === 'index-search') {
      query = this.$route.params[0]
    }

    (async () => {
      let posts = []
      let {data, error} = await api.getPosts(query)
      if (!error) {
        data.forEach((x) => posts.push({ id: x }))
        this.posts = posts
      }
    })()
  }
}
</script>

<style>
#index .thumb {
	float: left;
}
#index .clear {
	clear: both;
}
#index .thumb img {
	width: 100px;
}
</style>