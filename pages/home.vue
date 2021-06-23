<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <v-card>
        <v-card-title class="headline">
          <span>
            {{ user.name }}
          </span>
          <v-spacer />
          <span>Banco {{ bank.banco }}</span>
        </v-card-title>
        <v-card-subtitle>
          <span><strong style="font-size: 15px">Agencia: </strong>
            {{ bank.agencia }}
          </span>
          <span class="ml-5">
            <strong style="font-size: 15x">Conta:</strong> {{ bank.conta }}
          </span>
        </v-card-subtitle>
        <v-card-text class="mt-10">
          <v-row align="center" justify="center">
            <v-col cols="5">
              <p class="headline">
                Saldo: R${{ bank.saldo }}
              </p>
              <v-text-field
                v-model="valor"
                label="Valor"
                prefix="R$"
                outlined
              />
            </v-col>
          </v-row>
          <div class="mt-5">
            <v-row>
              <v-col>
                <v-row align="center" justify="center">
                  <v-btn
                    color="green"
                    class="ma-2 white--text"
                    @click="depositar(valor)"
                  >
                    Depositar
                    <v-icon right dark>
                      mdi-bank
                    </v-icon>
                  </v-btn>
                </v-row>
              </v-col>
              <v-col>
                <v-row align="center" justify="center">
                  <v-btn
                    color="red"
                    class="ma-2 white--text"
                    @click="sacar(valor)"
                  >
                    Sacar
                    <v-icon right dark>
                      mdi-cash
                    </v-icon>
                  </v-btn>
                </v-row>
              </v-col>
            </v-row>
          </div>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
export default {
  data () {
    return {
      valor: null,
      user: {
        id: '',
        name: '',
        email: '',
        cpf: '',
        sexo: '',
        phone: ''
      },
      bank: {
        id: '',
        agencia: '',
        banco: '',
        conta: '',
        identificador: '',
        saldo: null
      }
    }
  },
  created () {
    this.inicialize()
  },
  methods: {
    inicialize () {
      this.$axios
        .$get(`/bank/${this.$route.params.id}`)
        .then((response) => {
          this.user.name = response.user.name
          this.user.email = response.user.email
          this.user.cpf = response.user.cpf
          this.user.sexo = response.user.sexo
          this.user.phone = response.user.phone
          this.bank.id = response._id
          this.bank.agencia = response.agencia
          this.bank.banco = response.bank
          this.bank.conta = response.conta
          this.bank.identificador = response.identificador
          this.bank.saldo = response.saldo
          console.log('Chegando', response)
        })
        .catch(() => {
          console.log('ERRRORRRR')
        })
    },
    sacar () {
      if (this.valor != null || '') {
        this.$axios
          .$put(`/bank/sacar/${this.bank.id}`, {
            saldo: this.valor
          })
          .then(() => {
            this.inicialize()
          })
          .catch(() => {
            console.log('ERRRORRRR')
          })
      } else {
        console.log('ERRRORRRR')
      }
    },
    depositar () {
      if (this.valor != null || '') {
        this.$axios
          .$put(`/bank/deposit/${this.bank.id}`, {
            saldo: this.valor
          })
          .then(() => {
            this.inicialize()
          })
          .catch(() => {
            console.log('ERRRORRRR')
          })
      } else {
        console.log('ERRRORRRR')
      }
    }
  }
}
</script>
