<template>
  <v-card>
    <v-toolbar color="orange-lighten-1" dark>
      <v-toolbar-title> Cadastro de Doador</v-toolbar-title>
    </v-toolbar>

    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          v-model="doador.nome"
          :rules="nomeRules"
          label="Nome"
          required
        ></v-text-field>

        <v-text-field
          v-model="doador.email"
          :rules="emailRules"
          label="E-mail"
          required
        ></v-text-field>

        <v-text-field v-model="doador.telefone" label="Telefone"></v-text-field>

        <v-text-field v-model="doador.endereco" label="Endereço"></v-text-field>

        <v-btn :disabled="!valid" color="success" @click="submit">
          Salvar
        </v-btn>
      </v-form>

      <v-divider class="my-4"></v-divider>

      <v-data-table
        :headers="doacoesHeaders"
        :items="doacoes"
        :items-per-page="5"
        class="elevation-1"
      >
        <template #item.acao="{ item }">
          <v-btn icon color="red" @click.stop="deletarDoacao(item)">
            <v-icon>mdi-delete</v-icon>
          </v-btn>
        </template>
      </v-data-table>
    </v-container>
  </v-card>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    doador: {
      id: null,
      nome: "",
      email: "",
      telefone: "",
      endereco: "",
    },
    doacoes: [],
    nomeRules: [
      (v) => !!v || "Nome é obrigatório",
      (v) => (v && v.length >= 3) || "Nome precisa ter pelo menos 3 caracteres",
    ],
    emailRules: [
      (v) => !!v || "E-mail é obrigatório",
      (v) => /.+@.+\..+/.test(v) || "E-mail precisa ser válido",
    ],
    doacoesHeaders: [
      { text: "Data da Doação", value: "data" },
      { text: "Valor", value: "valor" },
      { text: "Ações", value: "acao", sortable: false },
    ],
  }),

  methods: {
    async submit() {
      try {
        const response = await this.$axios.post(
          "https://localhost:5000/api/doador",
          this.doador
        );
        this.doador = {
          id: null,
          nome: "",
          email: "",
          telefone: "",
          endereco: "",
        };
        this.carregarDoacoes();
      } catch (error) {
        console.error("Erro ao salvar doador:", error);
      }
    },

    async carregarDoacoes() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5000/api/doacoes"
        );
        this.doacoes = response.data;
      } catch (error) {
        console.error("Erro ao carregar doações:", error);
      }
    },

    async deletarDoacao(item) {
      try {
        await this.$axios.delete(
          `https://localhost:5000/api/doacoes/${item.id}`
        );
        this.carregarDoacoes();
      } catch (error) {
        console.error("Erro ao deletar doação:", error);
      }
    },
  },

  mounted() {
    this.carregarDoacoes();
  },
};
</script>

<style scoped>
.v-data-table {
  margin-top: 16px;
}
</style>
