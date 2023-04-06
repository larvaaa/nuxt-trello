<template>
  <div>
    <el-dialog :visible.sync="dialogFormVisible" :close-on-click-modal="false" :show-close="false">
      <el-form>
        <el-form-item label="title" :label-width="formLabelWidth">
          <el-input v-model="form.title" type="textarea" autosize placeholder="Please input"></el-input>
        </el-form-item>
        <el-form-item label="description" :label-width="formLabelWidth">
          <el-input v-model="form.description" type="textarea" :autosize="{ minRows: 4, maxRows: 10}" placeholder="Please input"></el-input>
        </el-form-item>
        <!-- <input type="file" multiple /> -->
        <el-upload
          class="upload-demo"
          action=""
          multiple
          :auto-upload="false"
          :on-remove="removeFile"
          :on-change="addFile"
          :on-exceed="exceed"
          :limit="3">
          <el-button type="primary">find file</el-button>
        </el-upload>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" @click="saveCard()">Save</el-button>
        <el-button @click="dialogClose()">Cancel</el-button>
      </span>


    </el-dialog>
    
  </div>
</template>
<script>

export default {
  name: 'AddCardForm',
  components: {},
  props: [
   'todoId'
  ],
  data() {
    return {
      dialogFormVisible: true,
      form: {
        title: '' , 
        description: '',
        files: []
      },
      formLabelWidth: '120px',
      dialogImageUrl: '',
      disabled: false
    };
  },
  watch: {
  },
  mounted() {
  },
  methods: {
    exceed() {
      alert("3개까지 추가 가능합니다");
    },
    addFile() {
      const files = document.querySelector("input[type='file']").files;
      for(const file of files) {
        let bool = true;
        for(const file2 of this.form.files) {
          if(file.name === file2.name) bool = false;
        }
        if(bool) this.form.files.push(file);
      }
    },
    removeFile(file) {
      for(let i = 0; i < this.form.files.length; i++) {
        if(file.name === this.form.files[i].name) this.form.files.splice(i,1);
      }
      
    },
    dialogClose() {
      this.$emit('closeCardForm');
    },
    handlePictureCardPreview(file) {
      this.dialogImageUrl = file.url;
      this.dialogVisible = true;
    },
    async saveCard() {
      
      if(this.form.title === '' || this.form.title === undefined || this.form.title === null) {
        alert("카드제목 입력 필수");
        return false;
      }

      const frm = new FormData();
      frm.append("todoId",this.todoId);
      frm.append("cardTitle",this.form.title);
      frm.append("description",this.form.description);

      for(const file of this.form.files) {
        frm.append("files",file);
      }
      await this.$axios.post(
        `http://localhost:3000/api/card`, 
        frm,
        {
          headers: {'Content-Type': 'multipart/form-data'}
        })
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("card 추가했습니다");
          this.$emit('reload');
        } else {
          alert("error");
        }
      });

      this.$emit('closeCardForm');
    }
  }
}
</script>