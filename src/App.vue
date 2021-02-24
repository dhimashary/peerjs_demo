<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    {{ peerID }}
    <p>Lorem ipsum dolor sit amet consectetur adipisicing elit. Earum perspiciatis eius quo dignissimos quaerat numquam adipisci enim, deserunt non, nihil autem necessitatibus iusto fugiat saepe, nostrum cupiditate animi fugit veniam?</p>
    <Room :peerID="peerID" :peer="peer"/>
  </div>
</template>
<script>
import Peer from 'peerjs'
import Room from './views/Room'

export default {
  name: 'App',
  data() {
    return {
      peerID: null,
      peer: null
    }
  },
  components: {
    Room
  },
  mounted () {
    var peer = new Peer()
    this.peer = peer
    this.peer.on('open', (id) => {
      console.log(id)
      this.peerID = id
    })
    this.peer.on('error', (err) => {
      console.log(err)
    })
    peer.on('error', (err) => {
      console.log(err)
    })
  }
}
</script>
<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
