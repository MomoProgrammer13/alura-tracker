<template>
  <Formulario @aoSalvarTarefa="salvarTarefa"/>
  <div class="lista">
    <Box v-if="listaEstaVazia">
      Você não está muito produtivo hoje :(
    </Box>
    <div class="field">
      <p class="control has-icons-left">
        <input class="input" type="text" placeholder="Digite para filtrar" v-model="filtro">
        <span class="icon is-small is-left">
          <i class="fas fa-search"></i>
        </span>
      </p>
    </div>
    <Tarefa v-for="(tarefa, index) in tarefas" :key="index" :tarefa="tarefa" @ao-tarefa-clicada="selecionarTarefa"/>
    <Modal :mostrar="tarefaSelecionada != null">
        <template v-slot:header>
          <p class="modal-card-title">Editando uma tarefa</p>
          <button @click="fecharModal" class="delete" aria-label="close"></button>
        </template>
        <template v-slot:body>
          <label for="descricaoDaTarefa" class="label">
            Descrição
          </label>
          <input type="text" class="input" v-model="tarefaSelecionada.descricao" id="nomeDoProjeto"/>
        </template>
        <template v-slot:footer>
          <button @click="alterarTarefa" class="button is-success">Salvar alterações</button>
          <button @click="fecharModal" class="button">Cancelar</button>
        </template>
    </Modal>
  </div>
</template>

<script lang="ts">
import {computed, defineComponent, ref, watchEffect} from 'vue';
import BarraLateral from '../components/BarraLateral.vue'
import Formulario from '../components/Formulario.vue'
import Tarefa from '../components/Tarefa.vue'
import Box from '../components/Box.vue'
import ITarefa from '../interfaces/ITarefa'
import {useStore} from "@/store";
import {ALTERAR_TAREFA, CADASTRAR_TAREFAS, OBTER_PROJETOS, OBTER_TAREFAS} from "@/store/tipo-acoes";
import Modal from "@/components/Modal.vue";

export default defineComponent({
  name: 'App',
  components: {
    Modal,
    Formulario,
    Tarefa,
    Box,
  },
  data() {
    return {
      tarefaSelecionada: null as ITarefa | null
    }
  },
  setup() {
    const store = useStore()
    store.dispatch(OBTER_TAREFAS)
    store.dispatch(OBTER_PROJETOS)

    const filtro= ref("")
    // const tarefas = computed(() => store.state.tarefas.filter(t => !filtro.value || t.descricao.includes(filtro.value)))

    watchEffect(() => {
      store.dispatch(OBTER_TAREFAS, filtro.value)
    })

    return {
      tarefas: computed(() => store.state.tarefas),
      store,
      filtro
    }
  },
  computed: {
    listaEstaVazia(): boolean {
      return this.tarefas.length === 0
    }
  },
  methods: {
    salvarTarefa(tarefa: ITarefa): void {
      this.store.dispatch(CADASTRAR_TAREFAS, tarefa)
    },
    selecionarTarefa(tarefa: ITarefa) {
      this.tarefaSelecionada = tarefa
    },
    fecharModal() {
      this.tarefaSelecionada = null
    },
    alterarTarefa() {
      this.store.dispatch(ALTERAR_TAREFA, this.tarefaSelecionada)
          .then(() => this.fecharModal())
    }
  }
});
</script>
