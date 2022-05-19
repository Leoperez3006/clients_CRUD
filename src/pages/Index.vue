<template>
  <q-page >
 <!-- client component. C is an array of clients. it is a required prop for the component -->
    <client :c="clients" @add-client="addClient" @delete-client="deleteClient" ref="deletedClient"></client>
  </q-page>
</template>

<script>
import { api } from "boot/axios";
import Client from 'components/Client.vue'

export default {
  name: 'PageIndex',
  components: {
    Client
  },
  data () {
    return{
      clients: []
    }

  },
  methods: {
    //adds client to clients array
    addClient(addedClient){
      this.clients.splice(this.clients.length, 0, addedClient);
    },
    //deletes client from clients array
    deleteClient(deletedClient){
      let index = this.clients.findIndex(client => client.id === deletedClient.id)
      this.clients.splice(index, 1)

    },

    // gets all clients from clients api
    getClients() {
    api.get('client', {
      headers: {
        'Content-Type': 'application/json'
      }
    })
      .then ( res => {
        this.clients = res.data.clients
        console.log('Clientes: ',res.data.clients )
      })
    }
  },


  mounted (){
    this.getClients() // it is executed when the component is mounted
  }
}
</script>
