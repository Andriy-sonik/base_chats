<template>
    <div class="center">
      <h1>EventSourcing</h1>
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
    name: "EventSourcing",
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
        const eventSource = new EventSource(`http://localhost:5000/connect`)
        eventSource.onmessage = (event) =>{
            const message = JSON.parse(event.data);
            this.messages=[message,...this.messages];
        }
      },
    },
  };
  </script>
  
  <style>
  </style>