<template>
  <div class="user-playlists">
      <h2> My playlist </h2>
     <div v-if="playlists">
       <ListView :playlists = 'playlists' />
     </div>
  <router-link class="btn-new" :to="{name : 'CreatePlaylist'}">  Create a new Playlist</router-link>

  </div>
</template>
<script>
import getCollection from '../../composables/getCollection'
import getUser from '../../composables/getUser'
import ListView from '../../components/ListView.vue'
export default {
components: {ListView},
setup(){
  const {user} = getUser()
  const {documents: playlists} = getCollection(
    'playlists',
    ['userId', '==', user.value.uid]
    )

  return { playlists }
}
}
</script>

<style scoped>
 .btn-new{
        background: green;
        color: white;
        border-radius: 8px;
        border: 0;
        padding: 8px 12px;
        font-weight: 600;
        cursor: pointer;
        display: inline-block;
    }
</style>