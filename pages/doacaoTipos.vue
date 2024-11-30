<template>
  <v-card>
    <v-toolbar color="blue-lighten-1" dark>
      <v-toolbar-title> Cadastro de Tipos de Doação</v-toolbar-title>
    </v-toolbar>

    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          v-model="tipoDoacao.descricao"
          :rules="descricaoRules"
          label="Descrição"
          required
        ></v-text-field>

        <v-btn :disabled="!valid" color="success" @click="submit">
          Salvar
        </v-btn>
      </v-form>

      <v-divider class="my-4"></v-divider>

      <v-data-table
        :headers="tiposDoacaoHeaders"
        :items="tiposDoacao"
        :items-per-page="5"
        class="elevation-1"
      >
        <template #item.acao="{ item }">
          <v-btn icon color="red" @click.stop="deletarTipoDoacao(item)">
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
    tipoDoacao: {
      id: null,
      descricao: "",
    },
    tiposDoacao: [],
    descricaoRules: [
      (v) => !!v || "Descrição é obrigatória",
      (v) =>
        (v && v.length >= 3) || "Descrição precisa ter pelo menos 3 caracteres",
    ],
    tiposDoacaoHeaders: [
      { text: "Descrição", value: "descricao" },
      { text: "Ações", value: "acao", sortable: false },
    ],
  }),

  methods: {
    async submit() {
      try {
        const response = await this.$axios.post(
          "https://localhost:5000/api/tipodoacao",
          this.tipoDoacao
        );
        this.tipoDoacao = {
          id: null,
          descricao: "",
        };
        this.carregarTiposDoacao();
      } catch (error) {
        console.error("Erro ao salvar tipo de doação:", error);
      }
    },

    async carregarTiposDoacao() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5000/api/tipodoacao"
        );
        this.tiposDoacao = response.data;
      } catch (error) {
        console.error("Erro ao carregar tipos de doação:", error);
      }
    },

    async deletarTipoDoacao(item) {
      try {
        await this.$axios.delete(
          `https://localhost:5000/api/tipodoacao/${item.id}`
        );
        this.carregarTiposDoacao();
      } catch (error) {
        console.error("Erro ao deletar tipo de doação:", error);
      }
    },
  },

  mounted() {
    this.carregarTiposDoacao();
  },
};
</script>

<style scoped>
.v-data-table {
  margin-top: 16px;
}
</style>
