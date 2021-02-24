<template>
  <div>
    <div class="container">
      <h1>{{ peerID }}</h1>
      <button class="btn btn-success" @click="getUserMedia">START SCREEN</button>
      <form @submit.prevent="connectToPeer" class="mb-5">
        <label>CONNECT TO PEER</label>
        <input type="text" v-model="connectToPeerId">
        <button class="btn btn-primary rounded" type="submit">CONNECT</button>
      </form>
      <div class="row">
        <video id="main-video" height="500px" width="500px" class="bg-primary"></video>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Room",
  props: ['peerID', 'peer'],
  data () {
    return {
      connectToPeerId: '',
      localStream: null,
      call: null,
      provider: false
    }
  },
  methods: {
    connectToPeer () {
      // console.log(this.peer.call)
      let getMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia
      
      getMedia({video: true, audio: true}, (stream)=>{
            this.localStream = stream;
            this.call = this.peer.call(this.connectToPeerId, stream)
        },(err)=>{
            console.log(err)
        })
    },
    setLocalStream(stream){
      let video = document.getElementById("main-video")
      video.srcObject = stream
      video.muted = true
      video.play()
    },
    getUserMedia() {
      let getMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia
      
      getMedia({video: true, audio: true}, (stream)=>{
            this.provider = true
            this.localStream = stream;
            this.setLocalStream(this.localStream) // embed local video
        },(err)=>{
            console.log(err)
        })
    }
  },
  mounted() {
    if(this.peer && this.provider) {
      console.log("PEER BERUBAH", this.peer)
      this.peer.on('call',(call)=>{
        console.log("TEST")
        call.answer(this.localStream)
      })
    }
  },
  watch: {
    call (newValue, oldValue) {
      if (newValue) {
        console.log(this.call, "<-- call berubah")
        this.call.on('stream', stream => {
          console.log("SOMEONE IS STREAMING")
          this.setLocalStream(stream)
        })
      }
    },
    provider (newValue, oldValue) {
      if (newValue) {
        console.log("PROVIDER & PEER BERUBAH", this.localStream)
        this.peer.on('call',(call)=>{
          console.log("call", call)
          console.log("TEST SOMEONE IS JOINING ROOM")
          call.answer(this.localStream)
        })
      }
    }
  }
}
</script>