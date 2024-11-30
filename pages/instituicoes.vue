<template>
  <v-card>
    <v-toolbar color="orange-lighten-1" dark>
      <v-toolbar-title> Cadastro das Instituições</v-toolbar-title>
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

        <v-btn color="success" class="mr-4" block @click="submit">
          Salvar
        </v-btn>
      </v-form>
    </v-container>

    <v-data-table
      :headers="headers"
      :items="instituicoes"
      :items-per-page="5"
      class="elevation-1"
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
    async submit() {
      try {
        if (this.instituicao.id) {
          await this.$axios({
            method: "PUT",
            url: `http://localhost:5258/Instituicao/${this.instituicao.id}`,
            data: this.instituicao,
          });
        } else {
          await this.$axios({
            method: "POST",
            url: "http://localhost:5258/Instituicao",
            data: this.instituicao,
          });
        }

        this.instituicao = {
          nome: "",
          responsavel: "",
          email: "",
          telefone: "",
          endereco: "",
        };

        this.carregarInstituicoes();
      } catch (error) {
        console.error("Erro ao salvar instituição:", error);
      }
    },

    async carregarInstituicoes() {
      try {
        const response = await this.$axios({
          method: "GET",
          url: "http://localhost:5258/Instituicao",
        });

        this.instituicoes = response.data;
      } catch (error) {
        console.error("Erro ao carregar instituições:", error);
      }
    },

    async deletarInstituicao(item) {
      try {
        await this.$axios({
          method: "DELETE",
          url: `http://localhost:5258/Instituicao/${item.id}`,
        });

        this.carregarInstituicoes();
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
