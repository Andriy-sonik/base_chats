<template>
  <div class="center">
    <h1>LongPulling</h1>
    <div>
      <div class="form">
        <input v-model="message" type="text" />
        <button @click="sendMessage">Відправити</button>
      </div>
      <div class="messages">
        <div v-for="mess of messages" class="message" :key="mess.id">
          {{ mess.message }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "LongPulling",
  data() {
    return {
      message: "",
      messages: [],
    };
  },
  async mounted() {
    await this.subscribe();
  },
  methods: {
    async sendMessage() {
      await axios.post("http://localhost:5000/new-messages", {
        message: this.message,
        id: Date.now(),
      });
    },
    async subscribe() {
      try {
        const { data } = await axios.get("http://localhost:5000/get-messages");
        this.messages = [data, ...this.messages];
        await this.subscribe();
      } catch (e) {
        setTimeout(() => {
          this.subscribe();
        }, 500);
      }
    },
  },
};
</script>

<style>
</style>