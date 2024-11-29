<template>
  <v-card>
    <v-toolbar color="white" flat>
      <v-btn icon light>
        <v-icon color="grey darken-2">mdi-arrow-left</v-icon>
      </v-btn>

      <v-toolbar-title class="grey--text text--darken-4">
        Instituições
      </v-toolbar-title>

      <v-spacer></v-spacer>
    </v-toolbar>

    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-text-field
          v-model="instituicao.nome"
          :rules="nomeRules"
          label="Nome da Instituição"
          required
        ></v-text-field>

        <v-text-field
          v-model="instituicao.responsavel"
          label="Responsável"
        ></v-text-field>

        <v-text-field
          v-model="instituicao.email"
          :rules="emailRules"
          label="E-mail"
        ></v-text-field>

        <v-text-field
          v-model="instituicao.telefone"
          label="Telefone"
        ></v-text-field>

        <v-text-field
          v-model="instituicao.endereco"
          label="Endereço"
        ></v-text-field>

        <v-btn :disabled="!valid" color="success" class="mr-4" @click="submit">
          Salvar
        </v-btn>
      </v-form>
    </v-container>

    <v-data-table
      :headers="headers"
      :items="instituicoes"
      :items-per-page="5"
      class="elevation-1"
      @click:row="editarInstituicao"
    >
      <template #item.acao="{ item }">
        <v-btn icon color="red" @click.stop="deletarInstituicao(item)">
          <v-icon>mdi-delete</v-icon>
        </v-btn>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
export default {
  data: () => ({
    valid: true,
    instituicao: {
      id: null,
      nome: "",
      responsavel: "",
      email: "",
      telefone: "",
      endereco: "",
    },
    nomeRules: [
      (v) => !!v || "Nome é obrigatório",
      (v) => (v && v.length >= 3) || "Nome precisa ter pelo menos 3 caracteres",
    ],
    emailRules: [
      (v) => !!v || "E-mail é obrigatório",
      (v) => /.+@.+\..+/.test(v) || "E-mail precisa ser válido",
    ],
    headers: [
      { text: "Nome", value: "nome" },
      { text: "Responsável", value: "responsavel" },
      { text: "E-mail", value: "email" },
      { text: "Telefone", value: "telefone" },
      { text: "Endereço", value: "endereco" },
      { text: "Ações", value: "acao", sortable: false },
    ],
    instituicoes: [],
  }),

  methods: {
    async carregarInstituicoes() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5258/Instituicao"
        );
        this.instituicoes = response.data;
      } catch (error) {
        console.error("Erro ao carregar instituições:", error);
      }
    },

    async submit() {
      try {
        if (this.instituicao.id) {
          // Editar instituição existente
          await this.$axios.put(
            `https://localhost:5258/Instituicao/${this.instituicao.id}`,
            this.instituicao
          );
        } else {
          // Criar nova instituição
          await this.$axios.post(
            "https://localhost:5258/Instituicao",
            this.instituicao
          );
        }

        this.instituicao = {
          id: null,
          nome: "",
          responsavel: "",
          email: "",
          telefone: "",
          endereco: "",
        };

        await this.carregarInstituicoes();
      } catch (error) {
        console.error("Erro ao salvar instituição:", error);
      }
    },

    editarInstituicao(item) {
      this.instituicao = { ...item };
    },

    async deletarInstituicao(item) {
      try {
        await this.$axios.delete(
          `https://localhost:5258/Instituicao/${item.id}`
        );
        await this.carregarInstituicoes();
      } catch (error) {
        console.error("Erro ao deletar instituição:", error);
      }
    },
  },

  mounted() {
    this.carregarInstituicoes();
  },
};
</script>

<style>
.v-data-table {
  margin-top: 16px;
}
</style>
