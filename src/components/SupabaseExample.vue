<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { supabase } from '../supabase'

const messages = ref<any[]>([])
const newMessage = ref('')

const fetchMessages = async () => {
  const { data, error } = await supabase
    .from('messages')
    .select('*')
    .order('created_at', { ascending: false })
  
  if (error) console.error('Error fetching messages:', error)
  else messages.value = data || []
}

const addMessage = async () => {
  if (!newMessage.value.trim()) return

  const { error } = await supabase
    .from('messages')
    .insert({ content: newMessage.value })

  if (error) console.error('Error adding message:', error)
  else {
    newMessage.value = ''
    await fetchMessages()
  }
}

onMounted(() => {
  fetchMessages()
})
</script>

<template>
  <div>
    <h2>Supabase Messages Example</h2>
    <form @submit.prevent="addMessage">
      <input v-model="newMessage" placeholder="Type a message" />
      <button type="submit">Send</button>
    </form>
    <ul>
      <li v-for="message in messages" :key="message.id">
        {{ message.content }}
      </li>
    </ul>
  </div>
</template>