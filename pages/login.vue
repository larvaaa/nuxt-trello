<template>
  <div class="relative flex items-top justify-center min-h-screen bg-gray-100 sm:items-center sm:pt-0">
    <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.1.2/dist/tailwind.min.css" rel="stylesheet">
    <div class="max-w-4xl mx-auto sm:px-6 lg:px-8">
      <div class="mt-8 bg-white overflow-hidden shadow sm:rounded-lg p-6">
        <el-form label-width="50px">
          <el-form-item label="email">
            <el-input v-model="email" />
          </el-form-item>
          <el-form-item label="password" >
            <el-input v-model="password" type="password" @keyup.native.enter="login"/>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="login">login</el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>
  </div>
</template>
<script>
// import axios from 'axios';

export default { 
  name: 'Login',
  components: {},
  data() {
    return {
      email: '',
      password: '',
      returnMsg: ''
    };
  },
  methods: {
    
    async login() {
      const email = this.email;
      const password = this.password;
      await this.$axios.post(
        `http://localhost:3000/api/login`, 
        {email, password},
        {"Content-Type":"application-json"})
      .then(res => {
        if(res.data.result_cd === 'S') {
          this.$router.push("/");
        } else {
          alert("일치하는 계정이 없습니다");
        }
      });
    }
  }
}
</script>