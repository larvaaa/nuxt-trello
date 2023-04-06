<template>
<div>
  <Child>
    <template #forFirst>
      <p>here is the first</p>
    </template>
    <template #forSecond>
      <p>here is the second</p>
    </template>
    <template>
      here is the third
    </template>
  </Child>
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