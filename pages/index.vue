<template>
  <v-main>
    <v-container
      class="py-2 px-2" fluid>
      <v-row>
        <v-col md="8" offset-md="2">
          <v-card>
            
            <v-subheader>
              <v-row>
                <v-col align='end'>
                  <v-btn outlined color="primary" dark class="">Novo Contato</v-btn>
                  <v-btn 
                    v-if="contacts.filter((c) => c.selected).length > 0" 
                    color="error"
                    @click="delete_many()"
                    class="">Excluir</v-btn>
                </v-col>
              </v-row>
            </v-subheader>

            <v-list two-line>
              <template v-for="contact in contacts">
                <v-list-item :key="contact.id">
                  <v-checkbox id="select_option" v-model="contact.selected" :value="true"></v-checkbox>
                  <v-list-item-avatar color="#D3D3D3">
                    <i v-if="contact.costumer_type_id == 2" class="fas fa-building" style="font-size:30px"></i>
                    <i v-if="contact.costumer_type_id == 1" class="fas fa-user" style="font-size:30px"></i>
                  </v-list-item-avatar>

                  <v-list-item-content>
                    <v-list-item-title>
                      <span id="name">{{ contact.name }}</span>
                      <span id="date">{{ contact.created_at}}</span>
                    </v-list-item-title>

                    <v-list-item-subtitle>
                      <div v-if="(contact.contacts) && (contact.contacts.length > 0)" > {{ contact.contacts[0].number }}</div>
                      <div>{{ contact.email }}</div>
                    </v-list-item-subtitle>
                  </v-list-item-content>
                </v-list-item>
              </template>
            </v-list>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </v-main>
</template>

<script>


export default {
  
  created(){
    this.$store.commit('update_title', 'Lista de Contatos');
  },

  data: () => ({
    contacts: []
  }),

  methods:{
    delete_many(){
      this.contacts.filter((c) => c.selected).forEach(async contact => {
        await this.$axios.$delete(`/api/v1/costumers/${contact.id}`)
                         .catch(error => { 
                           contact.selected = False
                           console.log(error.response)})});
      this.contacts = this.contacts.filter((c) => !c.selected)
    }
  },

  async asyncData({ $axios }) {
    const contacts = await $axios.$get('http://127.0.0.1:3333/api/v1/costumers') 
    return { contacts }
  }
}
</script>

<style>

#name {
  font-size: 20px;
}

#date {
    float: right;
    font-size: 14px;
    color: grey;
}

#select_option {
  font-size: 10px;
}

</style>