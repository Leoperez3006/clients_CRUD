<template>
  <div class="q-pa-md">
    <q-table
      grid
      :card-container-class="cardContainerClass"
      title="Clientes"
      :data="c"
      :columns="columns"
      row-key="name"
      :filter="filter"
      :pagination.sync="pagination"
      :rows-per-page-options="rowsPerPageOptions"
    >
            <template v-slot:top-right>
        <div class = "col q-pa-md q-gutter-sm">

          <q-btn
            color="blue"
            icon-right="add"
            label="Agregar un cliente"
            @click="modalAdd = true"
            no-caps
          />

        </div>
          <q-input

          borderless dense debounce="300" v-model="filter" placeholder="Buscar">
            <template v-slot:append>
              <q-icon name="search" />
            </template>
          </q-input>

      </template>

      <template v-slot:item="props">
        <div class="q-pa-md col-xs-12 col-sm-6 col-md-4">
          <q-card>
            <q-card-section class="text-center">
              <strong class="text-h4">Informacion del cliente</strong>
              <br>
            </q-card-section>
            <q-separator />
            <q-card-section >
              <div>
                 <strong>Nombre:</strong>
                {{ props.row.name }} {{ props.row.p_lastname }} {{ props.row.m_lastname }}</div>
            </q-card-section>
            <q-separator />
            <q-card-section >
              <div>
                  <strong>Direccion:</strong>
                  {{ props.row.address }} </div>
            </q-card-section>
            <q-separator />
            <q-card-section >
              <div>
                  <strong>Email:</strong>
                  {{ props.row.email }} </div>
            </q-card-section>
            <q-separator />
            <q-card-section >
              <div class = "row">
                  <div class="col flex flex-center q-pa-sm">
                    <q-btn color="red" icon-right="delete" label="borrar" @click="deleteClient(props.row)"/>
                  </div>
                  <div class="col flex flex-center q-pa-sm">
                    <q-btn color="blue" icon-right="edit" label="editar" @click="editClient(props.row)" />
                  </div>
              </div>
            </q-card-section>
          </q-card>
        </div>
      </template>
    </q-table>

  <q-dialog persistent q-dialog v-model="modalAdd">
    <q-card style="width: 700px; max-width: 80vw;">
      <q-card-section class="row items-center q-pb-none">
          <div class="text-h6"> Informacion del Cliente</div>
          <q-space />
          <q-btn  icon="close" flat round dense v-close-popup @click="client ={}"/>
      </q-card-section>
      <q-separator />
      <q-form>
        <q-card-section>
          <div class="row">
            <div class="col">
              <q-input
                class = "q-pa-md"
                outlined v-model="client.name"
                label="Nombre (s)"
                :rules="[val => !!val || 'Campo Obligatorio', val => val.length >= 5 || 'Mínimo  5 carácteres']" />
            </div>
          </div>
          <div class="row">
            <div class="col">
              <q-input
                class = "q-pa-md"
                outlined v-model="client.p_lastname"
                label="Apellido paterno"
                :rules="[val => !!val || 'Campo Obligatorio', val => val.length >= 5 || 'Mínimo  5 carácteres']" />
            </div>
            <div class="col">
              <q-input
                class = "q-pa-md"
                outlined v-model="client.m_lastname"
                label="Apellido materno"
                :rules="[val => !!val || 'Campo Obligatorio', val => val.length >= 5 || 'Mínimo  5 carácteres']" />
            </div>
          </div>
          <div class="row">
            <div class="col">
              <q-input
                class = "q-pa-md"
                outlined v-model="client.address"
                label="Direccion"
                :rules="[val => !!val || 'Campo Obligatorio', val => val.length >= 5 || 'Mínimo  5 carácteres']" />
            </div>
            <div class="col">
              <q-input
                class = "q-pa-md"
                outlined v-model="client.email"
                label="Correo electronico"
                :rules="[val => !!val || 'Campo Obligatorio', val => val.length >= 5 || 'Mínimo  5 carácteres']" />
            </div>
          </div>
        </q-card-section>
        <div class = "q-pa-md flex flex-center" >
          <q-btn
            type="submit"
            color="blue"
            icon-right="check"
            label="Guardar"
            @click="addOrUpdate()"
            class="text-h9"/>
        </div>
      </q-form>
    </q-card>
  </q-dialog>



  </div>
</template>

<script>
import { api } from "boot/axios";
import { useQuasar } from 'quasar'



export default {
  setup (){
    const $q = useQuasar()
  },

    props: {
    c: {
      type: Array,
      required: true
    }
    },
    data () {
      return {
        add: true,
        modalAdd: false,
        editedClient: '',
        client: {
          name:'',
          p_lastname: '',
          m_lastname: '',
          address:'',
          email:''
        },
        filter: '',
        pagination: {
          page: 1,
          rowsPerPage: this.getItemsPerPage()
        },
        columns: [
          { name: 'name', label: 'Name', field: 'name' },
          { name: 'p_lastname', label: 'Apellido Parterno', field: 'p_lastname' },
          { name: 'm_lastname', label: 'Apellido Materno', field: 'm_lastname' },
          { name: 'address', label: 'Direccion', field: 'address' },
          { name: 'email', label: 'email', field: 'email' }
        ]

      }
    },
  computed: {
    cardContainerClass () {
      if (this.$q.screen.gt.xs) {
        return 'grid-masonry grid-masonry--' + (this.$q.screen.gt.sm ? '3' : '2')
      }

      return void 0
    },
    rowsPerPageOptions () {
      if (this.$q.screen.gt.xs) {
        return this.$q.screen.gt.sm ? [ 3, 6, 9 ] : [ 3, 6 ]
      }
      return [ 3 ]
    }
  },
  watch: {
    '$q.screen.name' () {
      this.pagination.rowsPerPage = this.getItemsPerPage()
    }
  },
  methods: {
    getItemsPerPage () {
      const { screen } = this.$q
      if (screen.lt.sm) {
        return 3
      }
      if (screen.lt.md) {
        return 6
      }
      return 9
    },
     addOrUpdate(){
       if(this.add) {
          this.$emit('add-client', this.client)
          api.post('/client', {
            name: this.client.name,
            p_lastname: this.client.p_lastname,
            m_lastname: this.client.m_lastname,
            address: this.client.address,
            email: this.client.email
          })
          .then(res => {
            console.log(res)
            this.modalAdd = false

                        if (res.data.status  === 1){
              this.$q.notify({
              message: 'Usuario agregado correctamente',
              icon: 'done',
              color: 'green'
            })
            } else {
              this.$q.notify({
              message: 'Hubo un error, intente mas tarde',
              icon: 'alert',
              color: 'negative'
            })
            }

          })
       } else {
          api.patch('/client/' + this.client.id, {
            name: this.client.name,
            p_lastname: this.client.p_lastname,
            m_lastname: this.client.m_lastname,
            address: this.client.address,
            email: this.client.email
          })
          .then(res => {
            console.log(res)
            this.modalAdd = false
            this.client = {}
            if (res.data.status  === 1){
              this.$q.notify({
              message: 'Usuario editado',
              icon: 'done',
              color: 'green'
            })
            } else {
              this.$q.notify({
              message: 'Hubo un error, intente mas tarde',
              icon: 'alert',
              color: 'negative'
            })
            }
          })
       }
        console.log(this.client)


      },

      editClient(x){
        this.client =x
        this.add = false
        this.modalAdd = true
      },
      emit (x){
        this.$emit('delete-client', x)
      },
      deleteClient (x) {
      console.log(typeof(x.id),x.id)
      this.$q.dialog({
        title: '¿Eliminar?',
        message: '¿Desea eliminar a ' + x.name + '?',
        ok: {
          push: true,
          label: 'Si'
        },
        cancel: {
          push: true,
          color: 'negative',
          label: 'No'

        },
        persistent: true
      }).onOk(() => {
        if (x.id != null){
          this.$emit('delete-client', x)

        } else {
          this.$q.notify({
            message: 'Porfavor, recargue la pagina',
            icon: 'error',
            color: 'negative'
          })
        }
        api.delete('/client/'+x.id)
        .then(res => {
          console.log(res)
          if (res.data.message  =="client not found"){
            this.$q.notify({
            message: 'Hubo un error, intente mas tarde',
            icon: 'error',
            color: 'negative'
          })

          } else {
             this.$q.notify({
                message: 'Usuario eliminado',
                icon: 'done',
                color: 'green'
              })
          }
        })
      }).onCancel(() => {
        // console.log('>>>> Cancel')
      }).onDismiss(() => {
        // console.log('I am triggered on both OK and Cancel')
      })
    }
  }
}
</script>



