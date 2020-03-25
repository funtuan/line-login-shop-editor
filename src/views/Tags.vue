<template>
  <div class="shops">
    <transition-group name="el-zoom-in-top">
      <div v-for="(tag, index) in tagList"  v-bind:key="tag._id" >
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <span>{{ tag.name }}</span>
            <el-button style="float: right; padding: 3px 0" type="text" @click="deleteTag(tag._id, index)">刪除</el-button>
            <el-button style="float: right; padding: 3px 0" type="text" @click="editTag(tag)">編輯</el-button>
          </div>
          <div class="content">
            新增日期：{{ tag.changeRecord[0].time }}<br>
            新增人：{{ tag.changeRecord[0].name }}
          </div>
        </el-card>
      </div>
    </transition-group>
    <div @click="addTag()">
      <el-card class="box-card plus">
        +
      </el-card>
    </div>
    <el-dialog
      width="350px"
      :title="editTitle"
      :visible.sync="editDialog">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="標籤名稱" prop="name" :rules="[{ required: true, message: '不能為空'}]">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-button type="primary" @click="submitForm()">{{ editTitle }}</el-button>
      </el-form>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';
axios.defaults.withCredentials = true;
export default {
  name: 'Shops',
  data: () => {
    return {
      editTitle: '',
      editType: '',
      list: [],
      editDialog: false,

      tagList: [],
      tagListDialog: true,

      form: {},
    };
  },
  created: function() {
    this.getTagList();
  },
  methods: {
    deleteTag(_id, index) {
      this.list.splice(index, 1);
      axios.delete(`${this.APIHOST}/api/shop/${_id}`)
        .then((response) => {
        });
    },
    
    editTag(tag) {
      this.editTitle = '編輯';
      this.editType = 'put';
      this.form = {...tag};
      this.editDialog = true;
    },

    addTag() {
      this.editTitle = '新增';
      this.editType = 'post';
      this.form = {};
      this.editDialog = true;
    },

    putTag(tag) {
      delete tag.changeRecord;
      delete tag.__v;
      axios.put(`${this.APIHOST}/api/tag`, tag)
        .then((response) => {
          this.editDialog = false;
          this.getTagList();
        });
    },

    postTag(tag) {
      delete tag.changeRecord;
      delete tag.__v;
      delete tag._id;
      axios.post(`${this.APIHOST}/api/tag`, tag)
        .then((response) => {
          this.editDialog = false;
          this.getTagList();
        });
    },
  
    getTagList() {
      axios.get(`${this.APIHOST}/api/tag`)
        .then((response) => {
          this.tagList = response.data;
        });
    },

    submitForm() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          if (this.editType === 'post') {
            this.postTag(this.form);
          }
          if (this.editType === 'put') {
            this.putTag(this.form);
          }
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
  }
};
</script>

<style scoped>
  .shops {
    max-width: 500px;
    margin:0px auto;
  }
  .content {
    font-size: 12px;
    line-height: 1.8;
    text-align: left;
  }
  .box-card {
    margin: 30px 0;
  }
  .plus {
    font-size: 50px;
    color: rgb(121, 121, 121);
    cursor: pointer;
  }
</style>
