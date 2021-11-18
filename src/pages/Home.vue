<template>
  <section class="home">
    <!-- Список progress -->
    <AppProgress
      v-for="(data, index) in progressData"
      :key="index"
      :label="data.label"
      :max="200"
      :options="data.elementData"
    />
  </section>
</template>

<script>
import staticData from '@/db/data'
import AppProgress from '@/components/blocks/AppProgressComponent'

export default {
  name: 'HomePage',
  components: {
    AppProgress
  },
  data () {
    return {
      progressData: staticData
    }
  }
}
</script>

<style lang="scss" scoped>
.home {
  display: flex;
  flex-direction: column;
  padding: 100px;
}
</style>
