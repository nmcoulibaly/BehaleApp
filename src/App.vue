<script setup>
import {ref, computed, onMounted, watch} from 'vue'
const sessions = ref([])
const content = ref('')
const checked = ref(null)
const numberPoints = ref(null)
const session_ascending = computed(() => sessions.value.sort((a,b) => {
  return b.createdAt - a.createdAt

}))
const addSession = () =>{
  if(content.value.trim() === '') {
    return
  }
  sessions.value.push({
    content: content.value,
    createdAt: new Date().getTime(),
    checked: checked.value,
    done: false,
  })
  content.value = '' 
  numberPoints = numberPoints.value += 5
}
const removeSession = session => {
  sessions.value = sessions.value.filter(s => s !== session)
  numberPoints = numberPoints.value -= 5
}
watch(sessions, (newValue) => {
  localStorage.setItem('sessions', JSON.stringify(newValue))
}, {deep: true})
onMounted(() => {
  sessions.value = JSON.parse(localStorage.getItem('sessions')) || []
})
watch(numberPoints, (newValue) => {
  localStorage.setItem('numberPoints', newValue)
})
onMounted(() =>{
  numberPoints.value = localStorage.getItem('numberPoints' )
})
</script>

<template>
  <main class="app">
    <img src="../public/icon.PNG" alt="behale">
    <section class="goal">
     <div class="header">
      <label>{{numberPoints}} Points</label>
      <label>{{session_ascending.length}} sessions</label>
      <label>1 week</label>
     </div> 
      <h2 class="title">
        Week Goal <input type="text" v-model="goal"/>
      </h2>
    </section>
    <section class="session-list">
      <h2>Sessions</h2>
      <div class="list">
        <div v-for="session in session_ascending" :class="`session-item ${session.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="session.done">
          </label>
          <div class="session-content">
            <input type="text" v-model="session.content" />
          </div>
          <div class="session-content">
            <input type="date" v-model="session.createdAt" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeSession(session)" v-if="!session.done">Delete</button>
          </div>
        </div>
      </div>
    </section>
    <section class="create-session">
      <form @submit.prevent="addSession">
      <input 
        type="text" 
        placeholder="Add a session" 
        v-model="content" />
        <input type="submit" value="Add Session">
      </form>
    </section>
  </main>
</template>

