<template>
  <div class="center">
    <h1>WebSock</h1>
    <div v-if="!connected" className="center">
      <div className="form">
        <input v-model="username" type="text" placeholder="Введіть ім`я" />
        <button @click="connect">Ввійти</button>
      </div>
    </div>
    <div v-else>
      <div class="form">
        <input v-model="message" type="text" />
        <button @click="sendMessage">Відправити</button>
      </div>
      <div class="messages">
        <div v-for="mess of messages" class="message" :key="mess.id">
          {{
            mess.event === "connection"
              ? `Пользователь ${mess.username} подключился`
              : `${mess.username}. ${mess.message}`
          }}
        </div>
      </div>
    </div>
  </div>
</template>
  
  <script>
export default {
  name: "WebSock",
  data() {
    return {
      message: "",
      messages: [],
      username: "",
      connected: false,
      socket: null,
    };
  },

  methods: {
    async sendMessage() {
      const message = {
        username: this.username,
        message: this.message,
        id: Date.now(),
        event: "message",
      };
      this.socket.send(JSON.stringify(message));
      this.message = "";
    },
    connect() {
      this.socket = new WebSocket("ws://localhost:5000");
      this.socket.onopen = () => {
        this.connected = true;
        const message = {
          event: "connection",
          username: this.username,
          id: Date.now(),
        };
        this.socket.send(JSON.stringify(message));
      };
      this.socket.onmessage = (event) => {
        const message = JSON.parse(event.data);
        this.messages = [message, ...this.messages];
      };
      this.socket.onclose = () => {
        console.log("Socket закрыт");
      };
      this.socket.onerror = () => {
        console.log("Socket произошла ошибка");
      };
    },
  },
};
</script>
  
  <style>
</style>