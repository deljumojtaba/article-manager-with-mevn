<template>

  <div>
    <v-dialog v-model="dialog" max-width="500px">
      <v-btn slot="activator" color="primary" dark class="mb-2">New user</v-btn>
      <v-card>
        <v-card-title>
          <span class="headline">{{ formTitle }}</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="editedItem.username" label="user name"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="editedItem.firstName" label="first name"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="editedItem.lastName" label="last name"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="editedItem.mobile" label="mobile"></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md4>
                <v-text-field v-model="editedItem.email" label="email"></v-text-field>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="close">Cancel</v-btn>
          <v-btn color="blue darken-1" flat @click.native="save">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-data-table
      :headers="headers"
      :items="desserts"
      hide-actions
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td>{{ props.item.username }}</td>
        <td class="text-xs-right">{{ props.item.firstName }}</td>
        <td class="text-xs-right">{{ props.item.lastName }}</td>
        <td class="text-xs-right">{{ props.item.mobile }}</td>
        <td class="text-xs-right">{{ props.item.email }}</td>
        <td class="justify-center layout px-0">
          <v-btn icon class="mx-0" @click="editItem(props.item)">
            <v-icon color="teal">edit</v-icon>
          </v-btn>
          <v-btn icon class="mx-0" @click="deleteItem(props.item)">
            <v-icon color="pink">delete</v-icon>
          </v-btn>
           <v-btn icon class="mx-0" @click="resetPassword(props.item)">
            <v-icon color="green">lock</v-icon>
          </v-btn>
        </td>
      </template>
     
    </v-data-table>
  </div>

</template>

<script>
import PostService from '../services/PostService'
import AuthenticationService from '../services/AuthenticationService'
export default {

    data: () => ({
      dialog: false,
      headers: [
        {
          text: 'USER NAME',
          align: 'left',
          sortable: false,
          value: 'username'
        },
        { text: 'FIRST NAME', value: 'calories' },
        { text: 'LAST NAME', value: 'fat' },
        { text: 'MOBILE', value: 'carbs' },
        { text: 'EMAIL', value: 'protein' },
        { text: 'Actions', value: 'name', sortable: false }
      ],
      desserts: [],
      editedIndex: -1,
      edituser:'',
      editedItem: {
        username: '',
        firstName: 0,
        lastName: 0,
        mobile: 0,
        email: 0
      },
      defaultItem: {
        username: '',
        firstName: 0,
        lastName: 0,
        mobile: 0,
        email: 0
      }
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Item' : 'Edit Item'
      }
    },

    watch: {
      dialog (val) {
        val || this.close()
      }
    },

    created () {
      this.initialize()
    },
    async mounted () {
    const result =await PostService.getAllUser()
    if (result) {
      this.desserts = result
    }
    },

    methods: {
      initialize () {
       
      },

     editItem (item) {
        this.editedIndex = this.desserts.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
        this.edituser = this . editItem
      
      },

      deleteItem (item) {
        const index = this.desserts.indexOf(item)
        confirm('Are you sure you want to delete this item?') && this.desserts.splice(index, 1)
        PostService.deleteUser(item)
      },
      resetPassword(item) {
        PostService.resetPassword(item)
        .then(alert('password reseted'))
      },

      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        }, 300)
      },

      save () {
        if (this.editedIndex > -1) {
        this.edituser = Object.assign(this.desserts[this.editedIndex], this.editedItem)
         PostService.saveUser(this.edituser)
        Object.assign(this.desserts[this.editedIndex], this.editedItem)

          
        } else {
          this.desserts.push(this.editedItem)
        }
        this.close()
      }
    }
  


}
</script>

<style>

</style>