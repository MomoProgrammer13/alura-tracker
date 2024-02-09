<template>
  <div class="notificacoes">
    <article class="message" :class="contexto[noticacao.tipo]" v-for="noticacao in notificacoes" :key="noticacao.id">
      <div class="message-header">
        {{noticacao.titulo}}
      </div>
      <div class="message-body">
        {{noticacao.texto}}
      </div>
    </article>
  </div>
</template>

<script lang="ts">

import {computed, defineComponent} from "vue";
import {useStore} from "@/store";
import {TipoNotificacao} from "@/interfaces/INotificacao";

export default defineComponent({
  name: 'Notificacoes',
  data(){
    return{
      contexto:{
        [TipoNotificacao.SUCESSO]: 'is-success',
        [TipoNotificacao.ATENCAO]: 'is-warning',
        [TipoNotificacao.FALHA]: 'is-danger',
      }
    }
  },
  setup(){
    const store = useStore()
    return {
      notificacoes: computed(() => store.state.notificacoes)
    }
  }
})

</script>

<style scoped>
.notificacoes{
  position: absolute;
  right: 0;
  width: 300px;
  padding: 8px;
  z-index: 105;
}
</style>
