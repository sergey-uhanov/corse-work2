<template>
  <li class="list-item">
    <div>
      <p><strong>{{eMail}}</strong></p>
      <small>{{commentText}}</small>
    </div>
  </li>
</template>

<script>
export default {
  props:{
    eMail:{
      type: String,
      required: true,
    },
    commentText:{
      type: String,
      required: true,
    }
  }
}
</script>

<style lang="scss" scoped>

</style>
