<template>
  <div>1
    <el-dialog :visible.sync="dialogFormVisible" :close-on-click-modal="false" :show-close="false">
      <el-form>
        <el-form-item label="title" :label-width="formLabelWidth">
          <el-input v-model="form.cardTitle" type="textarea" autosize placeholder="Please input"></el-input>
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
          :file-list="form.fileList"
          name="oriFileName"
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
  name: 'UpdCardForm',
  components: {},
  props: [
   'cardId'
  ],
  data() {
    return {
      form: {
        
      },
      removeFiles: [],
      dialogFormVisible: true,
      formLabelWidth: '120px',
      dialogImageUrl: '',
      disabled: false
    };
  },
  watch: {

  },
  mounted() {
    
    if(this.cardId === '' || this.cardId == null || this.cardId === undefined) return false;

    this.$axios.get(
    `http://localhost:3000/api/card/${this.cardId}`, 
    {},
    {"Content-Type":"application-json"})
    .then(res => {
      if(res.data.resultCd === 'S') {
        const files = [];
        for(const file of res.data.card.fileList) {
          file.name = file.oriFileName;
          files.push(file);
        }
        delete res.data.card.fileList;
        res.data.card.fileList = files;
        this.form = res.data.card;
        
      } else {
        alert("error");
      }
    });
  },
  methods: {
    exceed() {
      alert("3개까지 추가 가능합니다");
    },
    addFile() {
      const files = document.querySelector("input[type='file']").files;
      for(const file of files) {
        let bool = true;
        for(const file2 of this.form.fileList) {
          if(file.name === file2.name) bool = false;
        }
        if(bool) this.form.fileList.push(file);
      }
    },
    removeFile(file) {
      for(let i = 0; i < this.form.fileList.length; i++) {
        if(file.name === this.form.fileList[i].name) {
          this.removeFiles.push(this.form.fileList[i].attachId);
          this.form.fileList.splice(i,1);
          break;
        }
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
      
      if(this.form.cardTitle === '' || this.form.cardTitle === undefined || this.form.cardTitle === null) {
        alert("카드제목 입력 필수");
        return false;
      }

      const frm = new FormData();
      frm.append("cardId",this.form.cardId);
      frm.append("cardTitle",this.form.cardTitle);
      frm.append("description",this.form.description);
      frm.append("removeFiles",this.removeFiles);

      for(const file of this.form.fileList) {
        console.log(typeof file);
        frm.append("files",file);
      }
      
      await this.$axios.patch(
        `http://localhost:3000/api/card/${this.cardId}`,
        frm,
        {
          headers: {'Content-Type': 'multipart/form-data'}
        })
      .then(res => {
        if(res.data.resultCd === 'S') {
          alert("card 수정했습니다");
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