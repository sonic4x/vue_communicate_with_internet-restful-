<template>
  <div id="app">
    <h3>Sticky messages</h3>
    <ol>
      <li v-for="message in messages">
        <button @click="deleteItem( message._id) ">Delete</button>
        <button @click="edit( message._id, message.text) ">edit</button>
        <input v-model="message.text">
      </li>
    </ol>
    <input v-model="toAdd">
    <button @click="add">add</button>
  

    <hr/>
    <h3>Who' s better: Socrates or Plato?</h3>
      <p>Technically, without Plato we wouldn' t have<br>
        much to go on when it comes to information about<br>
        Socrates. Plato ftw! </p>
    <form>
      <label>Write your comment: </label>
      <textarea v-model="commentMessage"></textarea>
      <button @click.prevent="submit">Send! </button>
    </form>
    <p>Server got: {{ commentResponse}} </p>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'app',
  data () {
    return {
      messages:[],
      toAdd: '',
      commentMessage:'',
      commentResponse: '...'

    }
  },
  created () {
    axios.get( 'http://localhost:3030/messages/' )
      .then( response => {
        this.messages = response.data.data
    })
  },
  mounted(){ //Processing a request before sending it out
    axios.interceptors.request.use( config => {
        const body = config.data.body.replace( /punk/i, '***' )
        config.data.body = body
        return config
    })
  },
  methods:{
    add () {
      axios.post( 'http://localhost:3030/messages/', {
        text: this.toAdd
      })
        .then( response => {
          if ( response.status === 201) {
            this.messages.push( response.data)
            this.toAdd = ''
          }
        })
    },
    deleteItem (id) {
      console.log( ' delete' )
      axios.delete( 'http://localhost:3030/messages/' + id)
        .then( response => {
        if ( response.status < 400) {
          this.messages.splice(
            this.messages.findIndex( e => e.id === id) , 1)
        }
        })
    } ,
    edit (id, text) {
      axios.put( 'http://localhost:3030/messages/' + id, {
        text
      })
      .then( response => {
        if ( response.status < 400) {
          console.info( response.status)
        }
      })
    },

    submit () {
      axios.post( 'http://jsonplaceholder.typicode.com/comments' ,
      {
        body: this.commentMessage
      })
      .then( response => {
        this.commentResponse = response.data
      })
    }

  }
}
</script>

<style>

</style>
