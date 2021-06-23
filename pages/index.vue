<template>
  <v-container>
    <v-data-table
      :headers="headers"
      :items="users"
      sort-by="calories"
      class="elevation-1"
      @click="
        $router.push({
          name: 'home',
          params: {
            type: 'teste'
          }
        })
      "
    >
      <template #top>
        <v-toolbar flat>
          <v-toolbar-title>Usuarios</v-toolbar-title>
          <v-divider class="mx-4" inset vertical />
          <v-spacer />
          <v-dialog v-model="dialog" max-width="500px">
            <template #activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                Cadastrar
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="headline">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.name"
                        label="Nome"
                      />
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.email"
                        label="E-mail"
                      />
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.cpf"
                        label="CPF"
                      />
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.sexo"
                        label="Sexo"
                      />
                    </v-col>
                    <v-col cols="12" sm="6" md="6">
                      <v-text-field
                        v-model="editedItem.phone"
                        label="Contato"
                      />
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer />
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">
                  Salvar
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template #[`item.actions`]="{ item }">
        <v-icon
          small
          class="mr-2"
          @click="detailsItem(item)"
        >
          mdi-pencil
        </v-icon>
      </template>
    </v-data-table>
  </v-container>
</template>
<script>
export default {
  layout: 'page',
  data: () => ({
    dialog: false,
    dialogDelete: false,
    headers: [
      {
        text: 'Nome',
        align: 'start',
        sortable: false,
        value: 'name'
      },
      { text: 'E-mail', value: 'email' },
      { text: 'CPF', value: 'cpf' },
      { text: 'Sexo', value: 'sexo' },
      { text: 'Contato', value: 'phone' },
      { text: 'Actions', align: 'center', value: 'actions', sortable: false }
    ],
    users: [],
    editedIndex: -1,
    editedItem: {
      name: '',
      email: '',
      cpf: '',
      sexo: '',
      phone: ''
    },
    defaultItem: {
      name: '',
      email: '',
      cpf: '',
      sexo: '',
      phone: ''
    }
  }),

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'Novo Usuário' : 'Edite o usuáro'
    }
  },

  watch: {
    dialog (val) {
      val || this.close()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    }
  },

  created () {
    this.initialize()
  },

  methods: {
    initialize () {
      this.$axios.$get('/user', {}).then((response) => {
        this.users = response
      })
    },
    close () {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete () {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save () {
      if (this.editedIndex > -1) {
        Object.assign(this.users[this.editedIndex], this.editedItem)
      } else {
        this.users.push(this.editedItem)
        this.$axios
          .post(
            '/user',
            {
              name: this.editedItem.name,
              email: this.editedItem.email,
              cpf: this.editedItem.cpf,
              sexo: this.editedItem.sexo,
              phone: this.editedItem.phone
            }
          )
          .then((response) => {
            this.bankCreated({ userID: response.data._id })
          })
          .catch(() => {
          })
      }
    },
    bankCreated (value) {
      console.log('TESTANDO', value)
      this.$axios
        .post(
          '/bank',
          {
            user: value.userID,
            bank: 'Los Grandes',
            agencia: '1234-5',
            identif: '0000-1',
            conta: '54321-1',
            saldo: 0
          }
        )
        .then(() => {
          this.close()
        })
        .catch(() => {
        })
    },
    detailsItem (item) {
      console.log('item de saida', item)
      this.$router.push({
        name: 'home',
        params: {
          id: item._id
        }
      })
    }
  }
}
</script>
