<template>
<div v-if="error" class="error">{{error}}</div>
<div v-if="playlist" class="playlist-details">
    <!-- playlist information -->
    <div class="playlist-info">
        <div class="cover">
            <img :src="playlist.coverUrl">
        </div>
        <h2> {{playlist.title}} </h2>
        <p class="username">Created by : {{playlist.userName}}</p>
        <p class="description">{{playlist.description}}</p>
        <button v-if="ownership" @click="handleDelete" class="delete-button">Delete playlist </button>
    </div>
    <!-- playlist songs -->
    <div class="song-list">
        <div v-if="!playlist.songs.length">No songs added in this plyalist yet </div>
        <addSong v-if="ownership" :playlist = "playlist" />

        <div v-for="song in playlist.songs" :key="song.id" class="single-song">
          <div class="details">
            <h3> {{song.title}} </h3>
            <p> {{song.artist}} </p>
          </div>
          <button v-if="ownership"  @click="handleDeleteSong(song.id)"> Delete </button>
        </div>
    </div>
</div>
</template>

<script>
import addSong from '../../components/AddSong.vue'
import { computed } from '@vue/runtime-core'
import getDocument from '../../composables/getDocument'
import getUser from '../../composables/getUser'
import useDocument from '../../composables/useDocument'
import {useRouter} from 'vue-router'
import useStorage from '../../composables/useStorage'
export default {
props: ['id'],
components : {addSong},
setup(props) {
const {error, document: playlist} = getDocument('playlists', props.id)
const {user} = getUser()
const router = useRouter()
const {deleteDoc, updateDoc} = useDocument('playlists', props.id)
const {deleteImage} = useStorage()

const ownership = computed(()=>{
  return playlist.value && user.value && user.value.uid == playlist.value.userId
})

const handleDelete = async() =>{
    await deleteImage(playlist.value.filePath)
    await deleteDoc()
    router.push({name:'Home'})
}

const handleDeleteSong = async(id) =>{
 const songs = playlist.value.songs.filter((song) => song.id != id)
 await updateDoc({ songs })
}


return {error, playlist, ownership, handleDelete, handleDeleteSong}
}
}
</script>

<style scoped>
.playlist-details {
    display: grid;
    grid-template-columns: 1fr 2fr;
    gap: 80px;
  }
  .cover {
    overflow: hidden;
    border-radius: 20px;
    position: relative;
    padding: 160px;
  }
  .cover img {
    display: block;
    position: absolute;
    top: 0;
    left: 0;
    min-width: 100%;
    min-height: 100%;
    max-width: 200%;
    max-height: 200%;
  }
  .playlist-info {
    text-align: center;
  }
  .playlist-info h2 {
    text-transform: capitalize;
    font-size: 28px;
    margin-top: 20px;
  }
  .playlist-info p {
    margin-bottom: 20px;
  }
  .username {
    color: #999;
  }
  .description {
    text-align: left;
  }
  .delete-button{
       background-color: red;
       color : white;
       position: absolute;
       left: 300px;
       bottom: 35px;
   }
  .delete-button:hover{
       background-color: red;
       color : white; 
   }
  .single-song{
     padding: 10px 0 ;
     display: flex;
     justify-content: space-between ;
     align-items: center ;
     border-bottom: 1px dashed var(--secondary) ;
     margin-bottom: 20px ;
     margin-top: 30px;
   }
</style>