<template>
  <div>
    <Message :message="message" v-show="message" />
    <div>
      <form id="burger-form" @submit="createBurger">
        <div class="input-container">
          <label for="name">Your name*</label>
          <input
            type="text"
            id="name"
            name="name"
            v-model="name"
            placeholder="Type your name"
            required
          />
        </div>

        <div class="input-container">
          <label for="bread">Bread type*</label>
          <select id="bread" name="bread" v-model="bread" required>
            <option v-for="bread in breads" :key="bread.id" :value="bread.type">
              {{ bread.type }}
            </option>
          </select>
        </div>

        <div class="input-container">
          <label for="meat">Burger meat*</label>
          <select id="meat" name="meat" v-model="meat" required>
            <option v-for="meat in meats" :key="meat.id" :value="meat.type">
              {{ meat.type }}
            </option>
          </select>
        </div>

        <div id="optional-list-container" class="input-container">
          <label id="optional-list-title" for="optional_list"
            >Choose optional items:</label
          >
          <div
            class="checkbox-container"
            v-for="optional in optional_list"
            :key="optional.id"
          >
            <input
              type="checkbox"
              name="optional-list"
              v-model="optional_data"
              :value="optional.type"
            />
            <span>{{ optional.type }}</span>
          </div>
        </div>

        <div class="input-container">
          <input type="submit" class="submit-btn" value="Create your burger" />
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";
export default {
  name: "BurgerForm",
  components: {
    Message,
  },
  data() {
    return {
      name: null,
      bread: null,
      meat: null,
      optional_data: [],

      breads: null,
      meats: null,
      optional_list: null,

      message: null,
    };
  },
  methods: {
    async getIngredients() {
      const req = await fetch("http://localhost:3000/ingredients");
      const data = await req.json();

      this.breads = data.breads;
      this.meats = data.meats;
      this.optional_list = data.optional_list;
    },
    async createBurger(e) {
      e.preventDefault();

      const data = JSON.stringify({
        name: this.name,
        meat: this.meat,
        bread: this.bread,
        optional: Array.from(this.optional_data),
        status: "Solicitado",
      });

      const req = await fetch("http://localhost:3000/burgers", {
        method: "POST",
        headers: { "Content-Type": "application/json" },
        body: data,
      });

      const res = await req.json();

      this.message = `Order N° ${res.id} created`;

      setTimeout(() => (this.message = null), 3000);

      this.name = "";
      this.meat = "";
      this.bread = "";
      this.optional_data = "";
    },
  },
  mounted() {
    this.getIngredients();
  },
};
</script>

<style scoped>
#burger-form {
  max-width: 400px;
  margin: 0 auto;
}

.input-container {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
  color: #222;
}

label {
  font-weight: bold;
  margin-bottom: 15px;
  color: #222;
  padding: 5px 10px;
  border-left: 4px solid #fcba03;
}

input,
select {
  padding: 5px 10px;
  width: 300px;
}

#optional-list-container {
  flex-direction: row;
  flex-wrap: wrap;
}

#optional-list-title {
  width: 100%;
}

.checkbox-container {
  display: flex;
  align-items: flex-start;
  width: 50%;
  margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
  width: auto;
}

.checkbox-container span {
  margin-left: 6px;
  font-weight: bold;
}

.submit-btn {
  background-color: #222;
  color: #fcba03;
  font-weight: bold;
  border: 2px solid #222;
  padding: 10px;
  font-size: 16px;
  margin: 0 auto;
  cursor: pointer;
}
.submit-btn:hover {
  background-color: transparent;
  color: #222;
}
</style>
