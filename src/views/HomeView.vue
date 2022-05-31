<template>
  <table />
</template>

<script>
import Table from '@/components/Table.vue'
  // import HelloWorld from '../components/HelloWorld'


  export default {
  components: { Table },
    name: 'Home',

    components: () => import('@/components/Table'),
  }
</script>
