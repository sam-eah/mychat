<template>
  <v-container>
    <v-row justify="center" align="center">
      <v-col cols="12" sm="11" md="10" lg="8" xl="6">
        <div v-if="logged">
          <div style="height: 500px;overflow: auto;" class="msgHistory">
            <p
              v-for="message in messages"
              :key="message.id"
            >{{ message.user }} : {{ message.message }}</p>
          </div>
          <v-textarea
            @keyup.enter="saveMessage"
            full-width
            aria-multiline
            auto-grow
            outlined
            rows="1"
            max-height="50"
            v-model="message"
          ></v-textarea>
        </div>
        <div v-else>
          <v-text-field full-width v-model="user"></v-text-field>
          <v-btn @click="logged = true">Connect</v-btn>
        </div>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: "PrivateChat",
  data() {
    return {
      user: null,
      logged: false,
      message: null,
      messages: [],
    };
  },
  methods: {
    scrollToBottom() {
      let box = document.querySelector(".msgHistory");
      box.scrollTop = box.scrollHeight;
    },
    saveMessage() {
      this.$fireStore
        .collection("chat")
        .add({
          message: this.message,
          createdAt: new Date(),
          user: this.user,
        })
        .then(() => {
          this.scrollToBottom();
        });
      this.message = null;
    },
    fetchMessages() {
      this.$fireStore
        .collection("chat")
        .orderBy("createdAt")
        .onSnapshot((querySnapshot) => {
          let allMessages = [];
          querySnapshot.forEach((doc) => {
            console.log(doc);
            allMessages.push({
              ...doc.data(),
              id: doc.id,
            });
          });
          this.messages = allMessages;
        })
    },
  },
  created() {
    this.fetchMessages();
  },
};
</script>
