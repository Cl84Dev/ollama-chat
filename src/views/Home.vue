<script setup>
import Chat from "../components/Chat.vue";
</script>

<template>
  <div class="main-container">
    <!-- <div class="sidebar"></div> -->
    <div class="chat">
      <Chat />
    </div>
  </div>
</template>

<style scoped>
.main-container {
  display: flex;
  flex-direction: row;
  width: 100%;
  height: 100%;
}

/* .sidebar {
  grid-area: sidebar;
  background-color: #0d1117;
  width: 20%;
} */

.chat {
  grid-area: chat;
  background-color: #0d1117;
  /* width: 80%; */
  width: 100%;
}
</style>
