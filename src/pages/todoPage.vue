<template>
  <q-page class="q-pa-lg bg-grey-3 column">
    <h5 class="q-mt-none">Agendamento</h5>
    <div class="row q-pa-sm bg-primary">
      <q-input
        color="teal"
        filled
        v-model="newDado"
        @key.enter="addDado"
        class="col"
        square
        bg-color="white"
        placeholder="Nova agenda"
      >
        <q-btn @click="addDado" round dense flat icon="add" />
        <template v-slot:prepend>
          <q-icon name="event" />
        </template>
      </q-input>
    </div>
    <q-list class="bg-white" separator bordered>
      <q-item
        v-for="(dado, index) in dados"
        :key="dado.title"
        @click="dado.done = !dado.done"
        :class="{ 'done bg-blue-1': dado.done }"
        clickable
        v-ripple
      >
        <q-item-section avatar>
          <q-checkbox
            v-model="dado.done"
            class="no-pointer-events"
            color="primary"
          />
        </q-item-section>
        <q-item-section>
          <q-item-label>{{ dado.title }}</q-item-label>
        </q-item-section>
        <q-item-section v-if="dado.done" side>
          <div class="row">
            <q-btn
              @click.stop="editDado(index)"
              flat
              round
              dense
              color="positive"
              icon="edit"
            />
            <q-btn
              @click.stop="deleteDado(index)"
              flat
              round
              dense
              color="negative"
              icon="delete"
            />
          </div>
        </q-item-section>
      </q-item>
    </q-list>
    <div v-if="!dados.length" class="no-tasks absolute-center">
      <q-icon name="check" size="75px" color="primary" />
      <div class="text-h6 text-primary text-center">caixa vazia</div>
    </div>
  </q-page>
</template>

<script>
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      newDado: "",
      dados: JSON.parse(localStorage.getItem("dados")) || [],
    };
  },

  methods: {
    deleteDado(index) {
      this.$q
        .dialog({
          title: "Confirmação",
          message: "Tem a ceteza que pretende apagar?",
          cancel: true,
          persistent: true,
        })
        .onOk(() => {
          this.dados.splice(index, 1);
          localStorage.setItem("dados", JSON.stringify(this.dados));
          this.$q.notify("Apagado com sucesso!");
        });
    },

    addDado() {
      this.dados.push({
        title: this.newDado,
        done: false,
      });
      (this.newDado = ""),
        localStorage.setItem("dados", JSON.stringify(this.dados));
    },

    editDado(index) {
      const newTitle = this.$q
        .dialog({
          title: "Editar Tarefa",
          prompt: {
            model: this.dados[index].title,
            type: "text",
          },
          cancel: true,
          persistent: true,
        })
        .onOk((data) => {
          this.dados[index].title = data;
          localStorage.setItem("dados", JSON.stringify(this.dados));
          this.$q.notify("Edição sucedida!");
        });
    },
  },
});
</script>

<style lang="scss">
.done {
  .q-item__label {
    text-decoration: line-through;
    color: #bbb;
  }
}

.no-tasks {
  opacity: 0.5;
}

.notify {
  color: aqua;
}
</style>
