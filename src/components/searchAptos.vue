<template>
  <div>
    <section>
      <h5 class="title">Pesquisar apartamentos</h5>
      <form @submit.prevent="fetchUsersSearch(form.apNumberSearch)">
        <input
          type="text"
          placeholder="Número do Apartamento"
          v-model="form.apNumberSearch"
        />
        <button type="submit">Pesquisar</button>
      </form>
    </section>

    <section>
      <h5 class="title">Apartamento encontrado</h5>
      <ul>
        <li v-for="user in usersSearch" :key="user.id">
          <p>Apartamento: {{ user.apNumber }}</p>
          <br />
          <p>Bloco: {{ user.bloco }}</p>
          <br />
          <small>Morador: {{ user.name }}</small>
          <br />
          <small>Contato: {{ user.phone }}</small>
          <br />
          <small>Veículo: {{ user.vehicle }}</small>
          <br />
          <small>Placa: {{ user.placa }}</small>
          <br />
          <small>Observação: {{ user.obs }}</small>
          <br />
          <a class="destroy" @click="deleteUser(user.id)"></a>
        </li>
      </ul>
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
  components: { },
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
        obs: '',
        apNumberSearch: ''
      }
    }
  },
  methods: {
    async fetchUsersSearch(apNumber: User['apNumberSearch']) {
      try {
        const { data } = await axios.get(`/users/${apNumber}`)
        this.usersSearch = data
      } catch (error) {
        console.warn(error)
      }
    },
    async deleteUser(id: User['id']) {
      try {
        await axios.delete(`/users/${id}`)

        const indexUser = this.users.findIndex((user) => user.id === id)

        this.users.splice(indexUser, 1)
        this.usersSearch.splice(0, 1)
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
  grid-gap: 1rem;
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

.edit {
  background-color: #3218c0;
  width: 24px;
  height: 24px;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s linear;
  position: absolute;
  top: 65%;
  transform: translateY(-35%);
  right: 1.3rem;
}

.edit:before,
.edit:after {
  font-family: 'Font Awesome 5 Free';
  content: '\f007';
  border-radius: 1rem;
  position: absolute;
  top: 50%;
  left: 50%;
}

.edit:before {
  transform: translate(-50%, -50%);
}

.edit:after {
  transform: translate(-50%, -50%);
}

.edit:hover {
  background-color: #4029c2;
}
</style>
