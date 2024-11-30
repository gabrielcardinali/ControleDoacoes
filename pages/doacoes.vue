<template>
  <v-card>
    <v-toolbar color="green-lighten-1" dark>
      <v-toolbar-title> Cadastro de Doações</v-toolbar-title>
    </v-toolbar>

    <v-container fluid>
      <v-form ref="form" v-model="valid" lazy-validation>
        <v-select
          v-model="doacao.doadorId"
          :items="doadores"
          item-value="id"
          item-text="nome"
          label="Selecione o Doador"
          required
        ></v-select>

        <v-select
          v-model="doacao.instituicaoId"
          :items="instituicoes"
          item-value="id"
          item-text="nome"
          label="Selecione a Instituição"
          required
        ></v-select>

        <v-select
          v-model="doacao.doacaoTipoId"
          :items="tiposDoacao"
          item-value="id"
          item-text="descricao"
          label="Selecione o Tipo de Doação"
          required
        ></v-select>

        <v-text-field
          v-model="doacao.valor"
          type="number"
          label="Valor da Doação"
          :rules="valorRules"
        ></v-text-field>

        <v-text-field
          v-model="doacao.quantidade"
          type="number"
          label="Quantidade de Itens"
        ></v-text-field>

        <v-textarea
          v-model="doacao.descricao"
          label="Descrição da Doação"
        ></v-textarea>

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
    doacao: {
      doadorId: null,
      instituicaoId: null,
      doacaoTipoId: null,
      valor: null,
      quantidade: null,
      descricao: "",
    },
    doadores: [],
    instituicoes: [],
    tiposDoacao: [],
    doacoes: [],
    valorRules: [(v) => !v || v > 0 || "Valor deve ser maior que zero"],
    doacoesHeaders: [
      { text: "Doador", value: "doador.nome" },
      { text: "Instituição", value: "instituicao.nome" },
      { text: "Tipo de Doação", value: "doacaoTipo.descricao" },
      { text: "Valor", value: "valor" },
      { text: "Quantidade", value: "quantidade" },
      { text: "Descrição", value: "descricao" },
      { text: "Ações", value: "acao", sortable: false },
    ],
  }),

  methods: {
    async submit() {
      try {
        const response = await this.$axios.post(
          "https://localhost:5000/api/doacao",
          this.doacao
        );
        this.doacao = {
          doadorId: null,
          instituicaoId: null,
          doacaoTipoId: null,
          valor: null,
          quantidade: null,
          descricao: "",
        };
        this.carregarDoacoes();
      } catch (error) {
        console.error("Erro ao salvar doação:", error);
      }
    },

    async carregarDoacoes() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5000/api/doacao"
        );
        this.doacoes = response.data;
      } catch (error) {
        console.error("Erro ao carregar doações:", error);
      }
    },

    async deletarDoacao(item) {
      try {
        await this.$axios.delete(
          `https://localhost:5000/api/doacao/${item.id}`
        );
        this.carregarDoacoes();
      } catch (error) {
        console.error("Erro ao deletar doação:", error);
      }
    },

    async carregarDoadores() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5000/api/doador"
        );
        this.doadores = response.data;
      } catch (error) {
        console.error("Erro ao carregar doadores:", error);
      }
    },

    async carregarInstituicoes() {
      try {
        const response = await this.$axios.get(
          "https://localhost:5000/api/instituicao"
        );
        this.instituicoes = response.data;
      } catch (error) {
        console.error("Erro ao carregar instituições:", error);
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
  },

  mounted() {
    this.carregarDoadores();
    this.carregarInstituicoes();
    this.carregarTiposDoacao();
    this.carregarDoacoes();
  },
};
</script>

<style scoped>
.v-data-table {
  margin-top: 16px;
}
</style>
