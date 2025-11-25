<script setup>
import CommentItem from "../components/CommentItem.vue"
import NewComment from "../components/NewComment.vue"
import { db, updateDoc, increment } from "../firebase/init.js"

defineProps({
  post: {
    type: Object,
    required: true,
  }
});
const post = ref({ ...props.post })
async function updateCountPost() {
  const docRef = doc(db, "posts", props.post.id)
  await updateDoc(docRef, {
    countComments: increment(1)
  })
}
async function updateAvgStar(newStar) {
  const docRef = doc(db, "posts", post.value.id)

  const oldAvg = post.value.avgStars
  const oldCount = post.value.countComments
  const oldTotalStars = oldAvg * oldCount

  await updateDoc(docRef, {
    avgStars: (oldTotalStars + newStar) / (oldCount + 1)
  })
}



</script>

<template>
  <div class="box">
    <h4>user: {{ post.fullname }}</h4>
    {{ post.body }}
    <h4 class="title">comments ({{ post.countComments }})</h4>
    <h4 class="star">Avg star: ({{ post.avgStars }})</h4>
    <NewComment :docId="post.id" @add-comment="(star) => {
      updateCountPost();
      updateAvgStar(star);
    }" />
    <CommentItem v-for="comment in post.comments" :comment="comment" :key="comment.id" />
  </div>
</template>

<style scoped>
.box {
  border: 1px solid grey;
  padding: 5px;
  margin: 5px;
  border-radius: 5px;
}

.title {
  font-style: italic;
  color: hsla(160, 100%, 37%, 1);
}
</style>
