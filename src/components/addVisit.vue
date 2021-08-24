<template>
  <div>
    <section v-show="searching">
      <h5 class="title">Pesquisar visitante</h5>
      <form @submit.prevent="fetchOneVisit(form.nameUser)">
        <input
          type="text"
          placeholder="Nome do visitante"
          v-model="form.nameUser"
        />
        <button type="submit">Pesquisar</button>
      </form>
    </section>

    <section v-show="searching">
      <h5 class="title">Visitas encontradas</h5>
      <ul>
        <li v-for="visit in visitSearch" :key="visit.id">
          <p>Visitante: {{ visit.name }}</p>
          <br />
          <p>Data: {{ visit.data }}</p>
          <p>Hora: {{ visit.hora }}</p>
          <br />
          <small>Telefone: {{ visit.phone }}</small>
          <br />
          <small>Apartamento: {{ visit.apNumber }}</small>
          <br />
          <small>Bloco: {{ visit.bloco }}</small>
          <a class="destroy" @click="deleteVisit(visit.id)"></a>
        </li>
      </ul>
    </section>

    <section v-show="!searching">
      <form @submit.prevent="createVisit()">
        <input
          type="text"
          placeholder="Nome do visitante"
          v-model="form.name"
          required
        />
        <input
          type="text"
          placeholder="Telefone"
          v-model="form.phone"
          required
        />
        <input
          type="text"
          placeholder="Número do apartamento"
          v-model="form.apNumber"
          required
        />
        <input type="text" placeholder="Bloco" v-model="form.bloco" required />
        <input type="text" placeholder="Data" v-model="form.data" required />
        <input type="text" placeholder="Horário" v-model="form.hora" required />
        <button type="submit">Adicionar</button>
        <button type="button" @click="searching = true">Pesquisar</button>
      </form>
    </section>
    <section class="visitas" v-show="!searching">
      <h5 class="title">Visitas marcadas</h5>
      <ul>
        <li v-for="visit in visits" :key="visit.id">
          <p>Visitante: {{ visit.name }}</p>
          <br />
          <p>Data: {{ visit.data }}</p>
          <p>Hora: {{ visit.hora }}</p>
          <br />
          <small>Telefone: {{ visit.phone }}</small>
          <br />
          <small>Apartamento: {{ visit.apNumber }}</small>
          <br />
          <small>Bloco: {{ visit.bloco }}</small>
          <a class="destroy" @click="deleteVisit(visit.id)"></a>
        </li>
      </ul>
    </section>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from '@/utils/axios'

interface infoVisitas {
  id: string
  name: string
  phone: string
  apNumber: number
  bloco: string
  data: string
  hora: string
  nameUser: string
}

export default defineComponent({
  data() {
    return {
      visits: [] as infoVisitas[],
      visitSearch: [] as infoVisitas[],
      searching: false,
      form: {
        id: '',
        name: '',
        phone: '',
        apNumber: '',
        bloco: '',
        data: '',
        hora: '',
        nameUser: ''
      }
    }
  },
  async created() {
    await this.fetchVisits()
  },
  async mounted() {
    this.searching = false
  },
  methods: {
    async fetchOneVisit(nameUser: infoVisitas['nameUser']) {
      try {
        const { data } = await axios.get(`/visits/${nameUser}`)
        this.visitSearch = data
      } catch (error) {
        console.warn(error)
      }
    },
    async createVisit() {
      try {
        const { data } = await axios.post('/visits', this.form)

        console.log(data)
        this.visits.push(data)

        this.form.name = ''
        this.form.phone = ''
        this.form.apNumber = ''
        this.form.bloco = ''
        this.form.data = ''
        this.form.hora = ''
      } catch (error) {
        console.warn(error)
      }
    },
    async fetchVisits() {
      try {
        const { data } = await axios.get('/visits')
        this.visits = data
      } catch (error) {
        console.warn(error)
      }
    },
    async editVisit(id: infoVisitas['id']) {
      try {
        axios.post(`/visitas/${id}`, this.form)

        console.log(id)
        console.log(this.form)
      } catch (error) {
        console.warn(error)
      }
    },
    async deleteVisit(id: infoVisitas['id']) {
      try {
        await axios.delete(`/visits/${id}`)

        const indexUser = this.visits.findIndex((visit) => visit.id === id)

        this.visits.splice(indexUser, 1)
      } catch (error) {
        console.warn(error)
      }
    }
  }
})
</script>

<style scoped>
.container {
  margin: 4rem auto;
  max-width: 500px;
  width: 90%;
  display: grid;
  grid-gap: 2.5rem;
}

.title {
  font-size: 1rem;
  font-weight: 500;
  margin: 0.7rem 0;
}

form {
  display: grid;
  grid-template-columns: auto auto;
  grid-gap: 1rem;
}

label {
  font-size: 0.8rem;
  margin-left: 0.3rem;
  margin-bottom: -0.8rem;
}

input {
  background: transparent;
  border: 1px solid #999fc6;
  border-radius: 1rem;
  padding: 0.6rem;
  outline: none;
  color: #e1e8ef;
}

input::placeholder {
  color: #999fc6;
}

button {
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

p {
  margin: 0;
  color: white;
  text-transform:capitalize;
}

ul {
  padding: 0;
  margin: 0;
  display: grid;
  grid-gap: 1rem;
}

li {
  background-color: #2b3a4e;
  padding: 1.2rem 1rem;
  border-radius: 1rem;
  position: relative;
  list-style: none;
  color: #8b98a8;
}

.destroy {
  background-color: #d53e6b;
  width: 24px;
  height: 24px;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s linear;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 1.3rem;
}

.destroy:before,
.destroy:after {
  content: '';
  width: 3px;
  height: 13px;
  background-color: #ececf6;
  border-radius: 1rem;
  position: absolute;
  top: 50%;
  left: 50%;
}

.destroy:before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.destroy:after {
  transform: translate(-50%, -50%) rotate(130deg);
}

.destroy:hover {
  background-color: #984848;
}
</style>
