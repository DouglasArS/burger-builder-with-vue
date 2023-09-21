<template>
  <div id="burger-table">
    <Message :message="message" v-show="message" />
    <div>
      <div id="burger-table-heading">
        <div class="order-id">#:</div>
        <div>Client:</div>
        <div>Bread:</div>
        <div>Meet:</div>
        <div>Optional:</div>
        <div>Actions:</div>
      </div>
    </div>
    <div id="burger-table-rows">
      <div class="burger-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.name }}</div>
        <div>{{ burger.bread }}</div>
        <div>{{ burger.meat }}</div>
        <div>
          <ul>
            <li v-for="(opcional, index) in burger.optional" :key="index">
              {{ opcional }}
            </li>
          </ul>
        </div>
        <div>
          <select
            name="status"
            class="status"
            @change="updateBurger($event, burger.id)"
          >
            <option
              v-for="item_status in status"
              :key="item_status.id"
              :value="item_status.type"
              :selected="burger.status == item_status.type"
            >
              {{ item_status.type }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">
            Delete
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";
export default {
  name: "Dashboard",
  components: { Message },
  data() {
    return {
      burgers: null,
      burger_id: null,
      burger_status: null,
      status: [],
      message: null,
    };
  },
  methods: {
    async getOrders() {
      const req = await fetch("http://localhost:3000/burgers");
      const data = await req.json();

      this.burgers = data;

      this.getStatus();
    },
    async getStatus() {
      const req = await fetch("http://localhost:3000/status");
      const data = await req.json();

      this.status = data;
    },
    async deleteBurger(burgerId) {
      const req = await fetch(`http://localhost:3000/burgers/${burgerId}`, {
        method: "DELETE",
      });

      this.message = `Order N° ${burgerId} deleted`;

      setTimeout(() => (this.message = null), 3000);

      this.getOrders();
    },
    async updateBurger(event, burgerId) {
      const option = event.target.value;
      const data = JSON.stringify({ status: option });

      const req = await fetch(`http://localhost:3000/burgers/${burgerId}`, {
        method: "PATCH",
        headers: { "Content-Type": "application/json" },
        body: data,
      });

      this.message = `Order N° ${burgerId} update`;

      setTimeout(() => (this.message = null), 3000);

      this.getOrders();
    },
  },
  mounted() {
    this.getOrders();
  },
};
</script>

<style scoped>
#burger-table {
  max-width: 1200px;
  margin: 0 auto;
}

#burger-table-heading,
#burger-table-rows,
.burger-table-row {
  display: flex;
  flex-wrap: wrap;
}

#burger-table-heading {
  font-weight: bold;
  padding: 12px;
  border-bottom: 3px solid #333;
}

#burger-table-heading div,
.burger-table-row div {
  width: 19%;
}

.burger-table-row {
  width: 100%;
  padding: 12px;
  border-bottom: 1px solid #ccc;
}

#burger-table-heading .order-id,
.burger-table-row .order-number {
  width: 5%;
}

select {
  padding: 12px 6px;
  margin-right: 12px;
}

.delete-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
  transition: 0.5s;
}

.delete-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
