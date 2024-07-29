<template>
  <div class="chat-container">
    <div class="answer" ref="answer">
      <div class="inner-answer" v-for="message in chat">
        <div class="user" v-if="message.role === 'user'">
          {{ message.content }}
        </div>
        <div
          :class="$style['assistant']"
          v-if="message.role === 'assistant'"
          v-html="markdownToHTML(message.content)"
        ></div>
      </div>
    </div>
    <div class="question">
      <textarea v-model="question" @keyup.enter="ask"></textarea>
      <div class="ask-btn" @click="ask" v-if="!isLoading">Ask</div>
      <Spinner v-else />
    </div>
  </div>
</template>

<script setup>
import { ref, watch } from "vue";
import axios from "axios";
import { marked } from "marked";

import Spinner from "../components/Spinner.vue";

const question = ref("");
const chat = ref([]);
const answer = ref(null);
const isLoading = ref(false);

marked.use({
  gfm: true,
  async: false,
});

watch(
  chat,
  () => {
    answer.value.scrollTop = answer.value.scrollHeight + 20;
  },
  { deep: true }
);

const ask = async () => {
  if (!question.value || isLoading.value) {
    return;
  }
  isLoading.value = true;

  chat.value.push({
    role: "user",
    content: question.value,
  });

  question.value = "";

  chat.value.push({
    role: "assistant",
    content: "",
  });

  await axios({
    url: "http://localhost:11434/api/chat",
    data: {
      model: "llama3.1",
      messages: chat.value.slice(0, -1),
    },
    headers: {
      accept: "*",
      "content-type": "application/json",
    },
    method: "POST",
    onDownloadProgress: (progressEvent) => {
      const xhr = progressEvent.event.target;
      const { responseText } = xhr;

      let partialText = "";
      const parsedText = responseText.split("\n");
      parsedText.forEach((data) => {
        if (data) {
          partialText += JSON.parse(data).message.content;
        }
        chat.value[chat.value.length - 1].content = partialText;
      });
    },
  });

  isLoading.value = false;
};

const markdownToHTML = (md) => {
  return marked.parse(md);
};
</script>

<style>
.chat-container {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.answer {
  width: 100%;
  height: 80%;
  gap: 16px;
  overflow-y: auto;
}

.inner-answer {
  display: flex;
  flex-direction: column;
  width: 100%;
  padding: 16px;
}

.user {
  width: 80%;
  align-self: start;
  padding: 16px;
}

.question {
  width: 100%;
  height: 20%;
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 16px;
  padding: 16px;
}

.question textarea {
  height: auto;
  width: 80%;
  background-color: #161b22;
  border: none;
  outline: none;
  border-radius: 5px;
  padding: 8px;
  resize: none;
}

.ask-btn {
  width: 20%;
  height: 50px;
  border-radius: 5px;
  background-color: #343942;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1.1rem;
}

.ask-btn:hover {
  cursor: pointer;
  user-select: none;
  border: 1px solid #e6edf3;
}

.ask-btn:active {
  background-color: #161b22;
}
</style>

<style module>
.assistant {
  width: 80%;
  align-self: end;
  border-radius: 5px;
  background-color: #343942;
  padding: 16px;
  word-wrap: break-word;

  & ol,
  ul {
    margin-left: 32px;
  }
  & pre {
    background-color: #161b22;
    width: 100%;
    margin: 32px 0;
    padding: 32px;
    border-radius: 5px;
  }

  & code {
    font-family: "Courier New", Courier, monospace;
    font-size: 0.8rem;
    white-space: pre-wrap;
    width: 100%;
  }
}
</style>
