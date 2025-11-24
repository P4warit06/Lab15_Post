<script setup>
import { RouterLink, RouterView } from 'vue-router'
import { ref, onMounted } from 'vue'
import { query, collection, getDocs } from 'firebase/firestore'
import db from './firebase/init.js'
import UserList from './components/UserList.vue'

const users = ref([])

async function getUsers(){
  /*  add your code here */
  const usersRef = collection(db, "users")
  const querySnapshot = query(usersRef)
  const unsubscribe = onSnapshot(querySnapshot, (snapshot) => {
    users.value = []
    snapshot.forEach((doc => {
      let data = doc.data()
      data.id = doc.id
      users.value.push(data)
    }))
  })

  
}

onMounted(() => {
    getUsers() 
})
</script>

<template>
  <div>
    <div>
      <UserList :users="users" />
    </div>
  </div>
  <div class="content">
    <RouterView />
  </div>
</template>

<style scoped>

</style>

