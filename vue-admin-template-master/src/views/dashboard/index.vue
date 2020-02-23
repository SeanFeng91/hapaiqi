<template>
  <div class="dashboard-container">
    <!-- <div class="dashboard-text">namehhh: {{ name }}</div> -->
    <component :is="currentRole" />
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import adminDashboard from './admin/index.vue'

export default {
  name: 'Dashboard',
  components:{
    adminDashboard
  },
  data() {
    return {
      currentRole: 'adminDashboard'
    }
  },
  computed: {
      ...mapGetters([
        'name'
      ])
  },
    created() {
      if (!this.roles.includes('admin')) {
        this.currentRole = 'editorDashboard'
      }
    }

  // computed: {
  //   ...mapGetters([
  //     'name'
  //   ])
  // }
}
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
</style>
