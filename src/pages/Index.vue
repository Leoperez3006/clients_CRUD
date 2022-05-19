<template>
  <q-page >
    <!-- <client v-for="client in clients" :key="client.id" :c="client"></client> -->
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
    addClient(addedClient){
      console.log('added',addedClient)
      this.clients.splice(this.clients.length, 0, addedClient);
    },
    deleteClient(deletedClient){
      let index = this.clients.findIndex(client => client.id === deletedClient.id)
      this.clients.splice(index, 1)

    },



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
    console.log('peticion a la API')
    this.getClients()
  }
}
</script>
