<template>
<div>
<img v-for="(index) in happyCount" :key="index" :src="'./image/omyim.png'" style="height:30px;"/>
</div>
</template>

<script>

export default {
  name: 'Happy',
  props: {
    happy: {
      type: Number
    }
  },
  computed: {
      happyCount(){
          return this.happy
      }
  }
}
</script>

<style scoped>

</style>
