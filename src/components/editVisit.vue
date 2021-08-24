<template>
  <div>
    <section>
      <h5 class="title">Editar apartamento</h5>
      <form
        v-show="!searched"
        @submit.prevent="fetchUsersSearch(form.apNumberSearch)"
      >
        <input
          type="text"
          placeholder="Número do Apartamento"
          v-model="form.apNumberSearch"
        />
        <button type="submit">Pesquisar</button>
      </form>
      <form v-show="searched" @submit.prevent="editVisit(form.id)">
        <label for="">Apartamento:</label>
        <input
          type="text"
          placeholder="Número do Apartamento"
          v-model="form.apNumber"
        />
        <label for="">Bloco:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Bloco"
          v-model="form.bloco"
        />
        <label for="">Proprietário:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Proprietário"
          v-model="form.name"
        />
        <label for="">Telefone:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Telefone"
          v-model="form.phone"
        />
        <label for="">Veículo:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Veículo"
          v-model="form.vehicle"
        />
        <label for="">Placa:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Placa"
          v-model="form.placa"
        />
        <label for="">Observação:</label>
        <input
          v-show="searched"
          type="text"
          placeholder="Observação"
          v-model="form.obs"
        />
        <button type="submit">Salvar</button>
      </form>
    </section>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import axios from '@/utils/axios'

interface User {
  id: string
  name: string
  phone: string
  apNumber: number
  bloco: string
  vehicle: string
  placa: string
  obs: string
  apNumberSearch: string
}

export default defineComponent({
  data() {
    return {
      users: [] as User[],
      userEdit: [] as User[],
      searched: false,
      form: {
        id: '',
        name: '',
        phone: '',
        apNumber: '',
        bloco: '',
        vehicle: '',
        placa: '',
        obs: '',
        apNumberSearch: ''
      }
    }
  },
  methods: {
    async fetchUsersSearch(apNumber: User['apNumberSearch']) {
      try {
        const { data } = await axios.get(`/users/${apNumber}`)
        this.userEdit = data
        this.atInfos(data)
      } catch (error) {
        console.warn(error)
      }
    },
    async editVisit(id: User['id']) {
      try {
        axios.post(`/users/${id}`, this.form)

        console.log(id)
        console.log(this.form)
        
        this.searched = false
      } catch (error) {
        console.warn(error)
      }
    },
    async atInfos(data: User) {
      this.form.id = this.userEdit[0].id
      this.form.apNumber = this.form.apNumberSearch
      this.form.name = this.userEdit[0].name
      this.form.phone = this.userEdit[0].phone
      this.form.bloco = this.userEdit[0].bloco
      this.form.vehicle = this.userEdit[0].vehicle
      this.form.placa = this.userEdit[0].placa
      this.form.obs = this.userEdit[0].obs
      this.searched = true
      console.log(this.form)
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
