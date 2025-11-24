<script setup>
import { ref, onMounted, watch } from 'vue'
import { useRoute } from "vue-router"
import { collection, query, where, getDocs, onSnapshot } from "firebase/firestore"
import db from "../firebase/init.js"
import PostItem from "../components/PostItem.vue"

const user = ref("")
const posts = ref([])
const route = useRoute() 

async function getPosts(){
  user.value = route.params.user 
  /*  add your code here */
  const postRef = collection(db, "posts")
  const querySnapshot = query(postRef, where("user", "==", user.value))
  const unsubscribe = onSnapshot(querySnapshot, (snapshot) => {
    posts.value = []
    snapshot.forEach(async (doc) => {
      let data = doc.data()
      data.id = doc.id
      data.comments = []
      const commentRef = collection(db, "posts", doc.id, "comments")
      // const snapshot = await getAggregateFromServer(commentRef, {
      //   countOfDocs: count(),
      //   averageStars: average('stars')
      // })
      // data.countComment = snapshot.data().countOfDocs
      // data.averageStars = snapshot.data().averageStars
      const commentSnapshot = await getDocs(commentRef)
      commentSnapshot.forEach((comment) => {
        let commentData = comment.data()
        commentData.id = comment.id
        posts.value.comments = commentData
        data.comments.push(commentData)
      })
      posts.value.push(data)
    })
  })





}

watch( () => route.params.user, getPosts)

onMounted(() => {
  getPosts() 
})

</script>

<template>
    <h3>Posts : {{user}}</h3>
    <PostItem v-for="post in posts" :post="post" :key="post.id" />
</template>

<style scoped>

</style>

