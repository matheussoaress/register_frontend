<template>
  <div id="newCostumer">
    <v-form ref="form" v-model="valid" lazy-validation>
      <v-container 
        fluid
        class="grey lighten-5 py-8 px-6">
      <v-stepper v-model="e1">
        <v-stepper-header>
          <v-stepper-step
            :complete="e1 > 1"
            step="1"
          >
            Preenchimento dos Dados
          </v-stepper-step>

          <v-divider></v-divider>

          <v-stepper-step
            :complete="e1 > 2"
            step="2"
          >
            Confirmações
          </v-stepper-step>

        </v-stepper-header>
        <v-stepper-items>
          <v-stepper-content step="1">
            <v-row>
              <v-col cols="6">
                <v-text-field
                    name="name"
                    v-model="costumer.name"
                    label="Nome"
                    id="name"
                    :rules="[v => !!v || 'Nome é obrigatório']"
                    required></v-text-field>
                    
              </v-col>
              <v-col cols="6">
                <v-text-field
                    name="email"
                    v-model="costumer.email"
                    label="E-mail"
                    :rules="[v => !!v || 'E-mail é obrigatório', 
                             v => /.+@.+\..+/.test(v) || 'Digite e-mail válido']"
                    id="email"
                    required></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6">
                <v-select
                  name="costumer_type_id"
                  id="costumer_type_id"
                  label="Tipo do Cliente"
                  v-model="costumer.costumer_type_id"
                  :items="costumer_type_items"
                  item-text="name"
                  item-value="id"
                  :rules="[v => !!v || 'Selecione um tipo de pessoa']"
                  data-vv-name="costumer_type_id"
                  
                  required></v-select>
              </v-col>
              <v-col cols="6">
                <v-text-field v-if="costumer.costumer_type_id == 1"
                    name="document"
                    v-model="costumer.document"
                    label="Número do CPF"
                    id="document"
                    v-mask="'###.###.###-##'"
                    :rules="[v => !!v || 'CPF é obrigatório']"
                    required></v-text-field>
                <v-text-field
                    name="document"  v-if="costumer.costumer_type_id == 2"
                    v-model="costumer.document"
                    label="Número do CNPJ"
                    v-mask="'##.###.###/####-##'"
                    id="document"
                    :rules="[v => !!v || 'CNPJ é obrigatório']"
                    required></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols=12>
                <h3> Telefone </h3>
                <hr>
              </v-col>
            </v-row>
            <v-row v-for="(contact, index) in costumer.contacts" :key="index">
              
              <v-col cols=12 align='end'>
                <v-btn v-if="costumer.contacts.length > 1"
                  id="remove_contact"
                  @click="remove_contact(index)" > x </v-btn>
              </v-col>
              
              <v-col cols="6">
                <v-select name="contact_type_id"
                  id="contact_type_id"
                  label="Tipo do Telefone"
                  v-model="costumer.contacts[index].contact_type_id"
                  :items="contact_type_items"
                  item-text="name"
                  item-value="id"
                  data-vv-name="contact_type_id"
                  :rules="[v => !!v || 'Selecione um tipo de telefone']"
                  required></v-select>
              </v-col>
              <v-col cols="6">
                <v-text-field
                    name="number"
                    v-model="costumer.contacts[index].number"
                    label="Número"
                    v-mask="'(##) ###-###-###'"
                    :rules="[v => !!v || 'Número do telefone é obrigatório']"
                    id="number"
                    required></v-text-field>
              </v-col>
            </v-row>
            
            <v-row>
              <v-col cols=12 align='center'>
                <v-btn id="add_contact"
                  @click="add_contact()" > Adicionar Telefone </v-btn>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols=12>
                <h3> Endereço </h3>
                <hr>
              </v-col>
            </v-row>
            <!-- <v-row  v-for="(address, index) in costumer.addresses" :key="index"> -->
            <v-row  v-for="index in costumer.addresses.length" :key="index">
              
              <v-col cols=12 align='end'>
                <v-btn v-if="costumer.addresses.length > 1"
                  id="remove_address"
                  @click="remove_address(index-1)" > Apagar </v-btn>
              </v-col>
              
              <v-col cols="4">
                <v-text-field
                    name="zip_code"
                    v-model="costumer.addresses[index-1].zip_code"
                    label="CEP"
                    id="zip_code"
                    v-on:blur="find_zip_code(index-1)"
                    v-mask="'##.###-###'"
                    :rules="[v => !!v || 'CEP é obrigatório']"
                    required></v-text-field>
              </v-col>
              <v-col cols="8">
                <v-text-field
                    name="street"
                    v-model="costumer.addresses[index-1].street"
                    label="Logradouro"
                    id="street"
                    :rules="[v => !!v || 'Logradouro é obrigatório']"
                    required></v-text-field>
              </v-col>
              <v-col cols="2">
                
                <v-text-field
                    name="number"
                    v-model="costumer.addresses[index-1].number"
                    label="Número"
                    id="number"
                    :rules="[v => !!v || 'Número é obrigatório']"
                    required></v-text-field>
              </v-col>
              <v-col cols="10">
                <v-text-field
                    name="complement"
                    v-model="costumer.addresses[index-1].complement"
                    label="Complemento"
                    id="complement"></v-text-field>
              </v-col>
              <v-col cols="5">
                <v-text-field
                    name="neighborhood"
                    v-model="costumer.addresses[index-1].neighborhood"
                    label="Bairro"
                    id="neighborhood"
                    :rules="[v => !!v || 'Bairro é obrigatório']"
                    required></v-text-field>
              </v-col>
              <v-col cols="5">
                <v-text-field
                    name="city"
                    v-model="costumer.addresses[index-1].city"
                    label="Cidade"
                    id="city"
                    :rules="[v => !!v || 'Cidade é obrigatório']"
                    required></v-text-field>
              </v-col>
              <v-col cols="2">
                <v-text-field
                    name="state"
                    v-model="costumer.addresses[index-1].state"
                    label="Estado"
                    id="state"
                    :rules="[v => !!v || 'Estado é obrigatório']"
                    required></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols=12 align='center'>
                <v-btn v-if="costumer.addresses.length < 3"
                  id="add_address"
                  @click="add_address()" > Adicionar Endereço </v-btn>
              </v-col>
            </v-row>
            

            <v-btn
              color="primary"
              @click="validate()">
              Continue
            </v-btn>

            <v-btn text @click="reset ()">
              Cancelar
            </v-btn>
          </v-stepper-content>

          <v-stepper-content step="2">
            <v-row>
              <v-col cols="12">
                <v-file-input
                  label="Documento"
                  filled
                  prepend-icon="mdi-camera"
                  required
                ></v-file-input>
              </v-col>
            </v-row>

            <v-row>
              <v-col cols="12">
                <v-file-input
                  label="Comprovante de Endereço"
                  filled
                  prepend-icon="mdi-camera"
                  required
                ></v-file-input>
              </v-col>
            </v-row>

            <v-btn color="primary"
              @click="save_costumer()">
              Enviar
            </v-btn>

            <v-btn text
            @click="e1 = 1">
              Voltar
            </v-btn>
          </v-stepper-content>
        </v-stepper-items>
      </v-stepper>
    </v-container>
  </v-form>
  <v-alert type="error" v-if="alert"
  >{{error_message}}</v-alert>
  </div>
</template>

<script>

import Vue from 'vue'
import VueMask from 'v-mask'
Vue.use(VueMask);

export default {
  data(){
    return {
      e1: 1,
      valid: false,
      alert: false,
      error_message: '',
      costumer: {
        name: null, email: null, costumer_type_id: null,
        document: null,document: null,
        contacts: [{ contact_type_id: null, number: null }],
        addresses:[{
          zip_code: null, street: null, number: null, complement: null,
          neighborhood: null, city: null, state: null
        }]
      },
      costumer_type_items: [{id:1, name:'Pessoa Física'}, {id:2, name:'Pessoa Jurídica'}],
      contact_type_items: [{id: 1, name: 'Celular'}, {id: 2,  name:'Residencial'}]
    }
  },

  methods: {
      validate () {
        if (this.$refs.form.validate()){
          this.e1 = 2
        }
      },

      async save_costumer(){
        const costumer = await this.$axios
                                    .$post('/api/v1/costumers/',
                                      this.costumer,
                                      {headers: {'Content-Type': 'application/json', 
                                        'Access-Control-Allow-Origin': '*'}})
                                    .catch(error => {
                                      this.alert = true
                                      setTimeout(()=> {
                                        this.alert = false
                                      }, 5000)
                                      this.error_message = error.response.data[0].message})
        if (costumer){
          redirect('/');
        }

      },

      reset () {
        this.$refs.form.reset()
      },

      remove_contact(index) {
        this.costumer.contacts.splice(index, 1);
      },

      add_contact(){
        this.costumer.contacts.push({ contact_type_id: null, number: null })
      },

      remove_address(index) {
        this.costumer.addresses.splice(index, 1);
      },

      add_address(){
        this.costumer.addresses.push({zip_code: null, street: null, number: null, complement: null,
                                      neighborhood: null, city: null, state: null})
      },

      async find_zip_code(index){
        if (this.costumer.addresses[index].zip_code){
          const nums = this.costumer.addresses[index].zip_code.replace(/[^\d]+/g, '')
          if(nums.length == 8){
            const address = await this.$axios.$get(`https://viacep.com.br/ws/${nums}/json/`)
            if(address.error != true){
              this.costumer.addresses[index].street = address.logradouro
              this.costumer.addresses[index].complement = address.complemento
              this.costumer.addresses[index].neighborhood = address.bairro
              this.costumer.addresses[index].city = address.localidade
              this.costumer.addresses[index].state = address.uf
            }
          }  
        }
         
      }
    },

  created(){
    this.$store.commit('update_title', 'Novo Contato');
  }
}
</script>

<style>

</style>