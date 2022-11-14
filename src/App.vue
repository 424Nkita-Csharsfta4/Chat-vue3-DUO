<script setup>
import AgoraRTM from 'agora-rtm-sdk';
import { v4 as uuidv4 } from 'uuid';
import { ref, onMounted, nextTick, defineExpose } from 'vue';

const APP_ID = '452f99a0814b44d29d9a446ec20356fc';
const CHANNEL = 'wdj';
let client = AgoraRTM.createInstance(APP_ID);
let uid = uuidv4();
let text = ref('');
let messagesRef = ref(null);
let messages = ref([]);
let channel;

defineExpose({ messagesRef });

const appendMessage = async (message) => {
  messages.value.push(message);
  await nextTick();
  messagesRef.value.scrollTop =
    messagesRef.value.scrollHeight;
};

onMounted(async () => {
  await client.login({ uid, token: null });
  channel = await client.createChannel(CHANNEL);
  await channel.join();
  channel.on('ChannelMessage', (message, peerId) => {
    appendMessage({
      text: message.text,
      uid: peerId,
    });
  });
});

function sendMessage() {
  if (text.value === '') return;
  channel.sendMessage({ text: text.value, type: 'text' });
  appendMessage({
    text: text.value,
    uid,
  });
  text.value = '';
}
const mes = 'Чат только для 2 програмистов из ТТИИП';
</script>

<template>
  <div class="panel">
    <h2>{{mes}}</h2>
    <div class="messages" ref="messagesRef">
      <div class="inner">
        <div
          :key="index"
          v-for="(message, index) in messages"
          class="message"
        >
          <div v-if="message.uid === uid" class="user-self">
          Frontender <span>:</span>
          </div>
          <div v-else class="user-them">Bekender <span>:</span>
          </div>
          <div class="text">{{ message.text }}</div>
        </div>
      </div>
    </div>

    <form @submit.prevent="sendMessage">
      <input v-model="text" />
      <button><i class="bi bi-arrow-right-circle-fill"></i></button>
    </form>
  </div>
</template>

<style>
button:hover{
    background-color: rgb(41, 41, 62);
    border-radius: 70px;
    transition: 0.6s ease-in-out;
}
.bi-arrow-right-circle-fill:hover{
  color: rgb(244, 152, 32);
  transition: 0.6s ease-in-out;
}
span{
  color: black;
}
h2{
  color: rgb(251, 251, 251);
}
#app {
  padding: 2em;
  height: 100%;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
body{
  background-color: rgb(28, 15, 34);
  margin: 0;
  height: 400px;
}

.panel {
  display: flex;
  flex-direction: column;
  padding: 20px;
  margin: 0 auto;
  max-width: 300px;
  height: 400px;
  width: 400px;
  background: rgba(207, 54, 246, 0.7);
  box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
  backdrop-filter: blur(4px);
  border-radius: 10px;
  border: 1px solid rgba(255, 255, 255, 0.18);
}
.messages {
  height: 100%;
  width: 100%;
  overflow-y: scroll;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
  background-color: white;
}
.inner {
  padding: 10px;
}
.message {
  text-align: left;
  display: flex;
  margin-bottom: 6px;
}
.user-self {
  color: rgb(221, 255, 0);
}
.user-them {
  color: rgb(0, 81, 255);
}
form {
  position: relative;
  display: flex;
}
input {
  width: 100%;
  border: none;
  height: 20px;
  padding: 8px;
  border-top: 1px solid rgb(107, 104, 104);
  border-radius: 0px;
  outline: none;
}
button {
  border: none;
  outline: none;
  background: none;
  position: absolute;
  right: 3px;
  top: 4px;
  font-size: 24px;
}
</style>
