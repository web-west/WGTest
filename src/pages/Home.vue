<template>
  <section class="home">
    <AppProgress
      v-for="(data, index) in progressData"
      :key="index"
      :label="data.label"
      :max="100"
      :options="data.elementData"
    />
  </section>
</template>

<script>
import AppProgress from '../components/blocks/AppProgressComponent'
export default {
  name: 'HomePage',
  components: {
    AppProgress
  },
  data () {
    return {
      progressData: [
        {
          label: 'Risk of Loss',
          elementData: [
            {
              style: 'low',
              name: 'Low',
              value: 1
            },
            {
              style: 'middle',
              name: 'Middle',
              value: 39
            },
            {
              style: 'high',
              name: 'High',
              value: 60
            }
          ]
        },
        {
          label: 'Satisfaction',
          elementData: [
            {
              style: 'low',
              name: 'Low',
              value: 1
            },
            {
              style: 'middle',
              name: 'Middle',
              value: 8
            },
            {
              style: 'high',
              name: 'High',
              value: 10
            }
          ]
        },
        {
          label: 'Impact of Loss',
          elementData: [
            {
              style: 'low',
              name: 'Low',
              value: 20
            },
            {
              style: 'middle',
              name: 'Middle',
              value: 72
            },
            {
              style: 'high',
              name: 'High',
              value: 8
            }
          ]
        },
        {
          label: 'Performance',
          elementData: [
            {
              style: 'low',
              name: 'Low',
              value: 70
            },
            {
              style: '#bee5eb',
              name: 'Middle',
              value: 20
            },
            {
              style: '',
              name: 'High',
              value: 8
            }
          ]
        }
      ]
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
