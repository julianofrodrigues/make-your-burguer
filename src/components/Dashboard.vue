<template>
    <div id="burger_table" v-if="burgers">
      <div>
        <div id="burger_table_heading">
          <div class="order_id">#:</div>
          <div>Cliente:</div>
          <div>Pão:</div>
          <div>Carne:</div>
          <div>Opcionais:</div>
          <div>Ações:</div>
        </div>
      </div>
      <div id="burger_table_rows">
        <div class="burger_table_row" v-for="burger in burgers" :key="burger.id">
          <div class="order_number">{{ burger.id }}</div>
          <div>{{ burger.nome }}</div>
          <div>{{ burger.pao }}</div>
          <div>{{ burger.carne }}</div>
          <div>
            <ul v-for="(opcional, index) in burger.opcionais" :key="index">
              <li>{{ opcional }}</li>
            </ul>
          </div>
          <div>
            <select name="status" class="status" @change="updateBurger($event, burger.id)">
              <option :value="s.tipo" v-for="s in status" :key="s.id" :selected="burger.status == s.tipo">
                {{ s.tipo }}
              </option>
            </select>
            <button class="delete_btn" @click="deleteBurger(burger.id)">Cancelar</button>
          </div>
        </div>
      </div>
    </div>
    <div v-else>
      <h2>Não há pedidos no momento!</h2>
    </div>
  </template>
  <script>
    export default {
      name: "Dashboard",
      data() {
        return {
          burgers: null,
          burger_id: null,
          status: []
        }
      },
      methods: {
        async getPedidos() {
          const req = await fetch('http://localhost:3000/burgers')
          const data = await req.json()
          this.burgers = data
          // Resgata os status de pedidos
          this.getStatus()
        },
        async getStatus() {
          const req = await fetch('http://localhost:3000/status')
          const data = await req.json()
          this.status = data
        },
        async deleteBurger(id) {
          const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "DELETE"
          });
          const res = await req.json()
          this.getPedidos()
        },
        async updateBurger(event, id) {
          const option = event.target.value;
          const dataJson = JSON.stringify({status: option});
          const req = await fetch(`http://localhost:3000/burgers/${id}`, {
            method: "PATCH",
            headers: { "Content-Type" : "application/json" },
            body: dataJson
          });
          const res = await req.json()
          console.log(res)
        }
      },
      mounted () {
      this.getPedidos()
      }
    }
</script>
  
<style scoped>
    #burger_table {
      max-width: 1200px;
      margin: 0 auto;
    }
    #burger_table_heading,
    #burger_table_rows,
    .burger_table_row {
      display: flex;
      flex-wrap: wrap;
    }
    #burger_table_heading {
      font-weight: bold;
      padding: 12px;
      border-bottom: 3px solid #333;
    }
    .burger_table_row {
      width: 100%;
      padding: 12px;
      border-bottom: 1px solid #CCC;
    }
    #burger_table_heading div,
    .burger_table_row div {
      width: 19%;
    }
    #burger_table_heading .order_id,
    .burger_table_row .order_number {
      width: 5%;
    }
    select {
      padding: 12px 6px;
      margin-right: 12px;
    }
    .delete_btn {
      background-color: #222;
      color:#fcba03;
      font-weight: bold;
      border: 2px solid #222;
      padding: 10px;
      font-size: 16px;
      margin: 0 auto;
      cursor: pointer;
      transition: .5s;
    }

    .delete_btn:hover {
      background-color: transparent;
      color: #222;
    }  
</style>