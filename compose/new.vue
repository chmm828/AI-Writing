<template>
  <div></div>
</template>
<script>
export default {
  created () {
    const query = this.$route.query
    this.$router.push({
      path: '/compose',
      query: query
    })
  }
}
</script>
