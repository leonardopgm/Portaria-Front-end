<template>
  <div>
    <section>
      <h5 class="title">Novo registro</h5>
      <form @submit.prevent="createUser">
        <input
          type="text"
          id="apNumber"
          placeholder="Número do Apartamento"
          v-model="form.apNumber"
          required
        />
        <input
          type="text"
          id="bloco"
          placeholder="Bloco"
          v-model="form.bloco"
          required
        />
        <input
          type="text"
          id="proprietario"
          placeholder="Proprietário"
          v-model="form.name"
          required
        />
        <input
          type="text"
          id="telefone"
          placeholder="Telefone"
          v-model="form.phone"
          required
        />
        <input
          type="text"
          id="veiculo"
          placeholder="Veículo"
          v-model="form.vehicle"
          required
        />
        <input
          type="text"
          id="placa"
          placeholder="Placa"
          v-model="form.placa"
          required
        />
        <button type="submit">Adicionar</button>
        <button type="reset">Limpar</button>
      </form>
    </section>
    <section>
      <h5 class="title">Lista de apartamentos</h5>
      <ul>
        <li v-for="user in users" :key="user.id">
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
  visitante: string
  hora: string
  obs: string
  apNumberSearch: string
}

export default defineComponent({
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
        apNumberSearch: '',
        visitante: '',
        hora: '',
        obs: ''
      }
    }
  },
  async created() {
    await this.fetchUsers()
    //await this.filterAZ()
  },
  async updated() {
    //await this.filterAZ()
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
    async fetchUsers() {
      try {
        const { data } = await axios.get('/users')
        this.users = data
        await this.filterAZ()
      } catch (error) {
        console.warn(error)
      }
    },
    async createUser() {
      try {
        const { data } = await axios.post('/users', this.form)

        console.log(data)
        this.users.push(data)

        this.form.name = ''
        this.form.phone = ''
        this.form.vehicle = ''
        this.form.placa = ''
        this.form.apNumber = ''
        this.form.bloco = ''
        this.form.obs = ''
        await this.filterAZ()
      } catch (error) {
        console.warn(error)
      }
    },
    async deleteUser(id: User['id']) {
      try {
        await axios.delete(`/users/${id}`)

        const indexUser = this.users.findIndex((user) => user.id === id)

        this.users.splice(indexUser, 1)
      } catch (error) {
        console.warn(error)
      }
    },
    mascaraDeTelefone(telefone: string) {
      const telAtual = telefone
      const isCelular = telAtual.length === 9
      let telAjustado
      if (isCelular) {
        telAjustado = telAtual.replace(
          /(\d{5})(\d{4})/,
          function (regex: any, arg1: any, arg2: any) {
            return arg1 + '-' + arg2
          }
        )
        return (this.form.phone = telAjustado)
      }
    },
    filterAZ() {
      let usersMapped = this.users.map((user, i) => {
        return { index: i, user: user.apNumber }
      })

      usersMapped.sort(function (a, b) {
        return a.user - b.user
      })

      let result = usersMapped.map((user) => {
        return this.users[user.index]
      })

      this.users = result
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
