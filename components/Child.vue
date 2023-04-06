<template>
  <div>
    <slot name="forFirst"/>
    <h1>first</h1>
    <slot name="forSecond"/>
    <h1>second</h1>
    <slot/>
    <h1>third</h1>
  </div>
</template>
<script>

export default { 
  name: '',
  components: {},
  setup() {},
  data() {
    return {
      sampleData: ''
    };
  },
  created() {},
  mounted() {},
  unmounted() {},
  methods: {}
}
</script>