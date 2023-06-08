<template>
  <div class="app-container">
    <Header :headerText="data.headerText" :pText="data.pText" :p2Text="data.p2Text" />
    <div class="chat-container">
      <ChatHeader />
      <div class="msg-container">
        <MessageBubble
          v-for="(message, index) in conversation"
          :key="index"
          :message="message.content"
          :role="message.role"
          :isLoading="isLoading && message.content === 'Loading'"
        />
      </div>
      <UserInput @input="onInput" @click="onClick" />
    </div>
  </div>
</template>

<script>
import Header from "./Header.vue";
import ChatHeader from "./ChatHeader.vue";
import MessageBubble from "./MessageBubble.vue";
import UserInput from "./UserInput.vue";

export default {
  name: "App",
  components: {
    Header,
    ChatHeader,
    MessageBubble,
    UserInput,
  },
  data() {
    return {
      data: {
        headerText: "Hello hello ✨",
        pText: "I'm a cute chatbot!",
        // p2Text: "I can help you with your horoscope",
        conversation: [],
        isLoading: false,
      },
      conversation: [],
      isLoading: false,
    };
  },
  methods: {
    updateUserMessages(newMessage) {
      if (!newMessage) {
        return;
      }
      this.isLoading = true;

      let newConversation = [...this.conversation, { role: "user", content: newMessage }];
      this.conversation = [...newConversation];

      fetch("http://localhost:8088/chat", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ conversation: newConversation }),
      })
        .then((response) => response.json())
        .then((data) => {
          this.conversation = [
            ...newConversation,
            { role: "assistant", content: data },
          ];
          this.isLoading = false;
        });
    },
    showMessages() {
      return this.conversation.map((message, index) => (
        <MessageBubble
          :key="index"
          :message="message.content"
          :role="message.role"
          :isLoading="this.isLoading && message.content === 'Loading'"
        />
      ));
    },
    onInput(event) {
      if (event.key === "Enter") {
        const userInput = event.target.value;
        this.updateUserMessages(userInput);
        event.target.value = "";
      }
    },
    onClick() {
      const inp = document.getElementById("chat");
      const userInput = inp.value;
      this.updateUserMessages(userInput);
      inp.value = "";
    },
  },
};
</script>

<style scoped>
.app-container {
  /* styles for app container */
}

/* Add styles for other components here */
</style>
