<template>
  <div class="users">
    <div class="container">
      <button
        id="btnHome"
        type="button"
        @click.prevent="currentView = 'allAptos'"
      >
        Principal
      </button>
      <button
        id="btnVisits"
        type="button"
        @click.prevent="currentView = 'addVisit'"
      >
        Visitas
      </button>
      <button
        id="btnSearch"
        type="button"
        @click.prevent="currentView = 'searchAptos'"
      >
        Procurar
      </button>
      <button
        id="btnEdit"
        type="button"
        @click.prevent="currentView = 'editVisit'"
      >
        Editar
      </button>
      <transition
        name="fade"
        enter-active-class="animate__animated animate__fadeIn"
        leave-active-class="animate__animated animate__fadeOut"
        :appear="true"
        mode="out-in"
      >
        <component :is="currentView" />
      </transition>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from '@/utils/axios'
import 'animate.css'

import allAptos from '@/components/allAptos.vue'
import searchAptos from '@/components/searchAptos.vue'
import editVisit from '@/components/editVisit.vue'
import addVisit from '@/components/addVisit.vue'

interface User {
  id: string
  name: string
  phone: string
  apNumber: number
  bloco: string
  vehicle: string
  placa: string
  visitante: string
  hora: string
  apNumberSearch: string
}

export default defineComponent({
  components: { allAptos, searchAptos, editVisit, addVisit },
  data() {
    return {
      users: [] as User[],
      usersSearch: [] as User[],
      form: {
        name: '',
        phone: '',
        apNumber: '',
        bloco: '',
        vehicle: '',
        placa: '',
        visiante: '',
        hora: '',
        apNumberSearch: ''
      },
      currentView: 'allAptos'
    }
  },
  async created() {
    await this.fetchUsers()
    console.log(this.users)
  },
  methods: {
    async fetchUsers() {
      try {
        const { data } = await axios.get('/users')
        this.users = data
      } catch (error) {
        console.warn(error)
      }
    },
  }
})
</script>

<style scoped>
.animate__animated.animate__fadeIn, .animate__animated.animate__fadeOut {
  --animate-duration: 0.2s;
}
.container {
  margin: 4rem auto;
  max-width: 500px;
  width: 90%;
  display: grid;
  grid-template-columns: auto auto auto auto;
  grid-template-rows: auto auto;
  grid-template-areas:
    'button1 button2 button3 button4'
    'content content content content';
  grid-gap: 2.5rem;
  justify-content: space-between;
}

#btnHome {
  grid-area: button1;
}

#btnVisits {
  grid-area: button2;
}

#btnEdit {
  grid-area: button3;
}

#btnSearch {
  grid-area: button4;
}

button {
  width: max-content;
  background-color: #1247af;
  color: #e1e8ef;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  transition: all 0.3s linear;
  outline: none;
  cursor: pointer;
  box-shadow: 0 0 5px 3px rgba(45, 108, 234, 0.3);
}

button:hover {
  background-color: #1b5cdc;
}

* {
  grid-area: content;
}
</style>
