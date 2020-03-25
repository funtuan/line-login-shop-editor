<template>
  <div class="shops">
    <transition-group name="el-zoom-in-top">
      <div v-for="(shop, index) in list"  v-bind:key="shop._id" >
        <el-card class="box-card">
          <div slot="header" class="clearfix">
            <el-tag style="float: left;">{{ getTagName(shop.tag) }}</el-tag>
            <span>{{ shop.name }}</span>
            <el-button style="float: right; padding: 3px 0" type="text" @click="deleteShop(shop._id, index)">刪除</el-button>
            <el-button style="float: right; padding: 3px 0" type="text" @click="editShop(shop)">編輯</el-button>
          </div>
          <div class="content">
            地址：{{ shop.address }}<br>
            電話：{{ shop.phone }}<br>
            負責人：{{ shop.leader }}<br>
            {{ shop.note }}
          </div>
        </el-card>
      </div>
    </transition-group>
    <div @click="addShop()">
      <el-card class="box-card plus">
        +
      </el-card>
    </div>
    <el-dialog
      :title="editTitle"
      :visible.sync="editDialog">
      <el-form ref="form" :model="form" label-width="80px">
        <el-form-item label="類別" prop="tag" :rules="[{ required: true, message: '不能為空'}]">
          <el-select v-model="form.tag" placeholder="請選擇標籤">
            <el-option v-for="tag in tagList"  v-bind:key="tag._id" :label="tag.name" :value="tag._id"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="店家名稱" prop="name" :rules="[{ required: true, message: '不能為空'}]">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="店家地址" prop="address" :rules="[{ required: true, message: '不能為空'}]">
          <el-input v-model="form.address"></el-input>
        </el-form-item>
        <el-form-item label="聯絡方式" prop="phone" :rules="[{ required: true, message: '不能為空'}]">
          <el-input v-model="form.phone"></el-input>
        </el-form-item>
        <el-form-item label="負責人" prop="leader" :rules="[{ required: true, message: '不能為空'}]">
          <el-input v-model="form.leader"></el-input>
        </el-form-item>
        <el-form-item label="備註">
          <el-input type="textarea" v-model="form.note"></el-input>
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

      form: {},
    };
  },
  created: function() {
    this.getShopList();
    this.getTagList();
  },
  methods: {
    deleteShop(_id, index) {
      this.list.splice(index, 1);
      axios.delete(`${this.APIHOST}/api/shop/${_id}`)
        .then((response) => {
        });
    },

    editShop(shop) {
      this.editTitle = '編輯';
      this.editType = 'put';
      this.form = {...shop};
      this.editDialog = true;
    },

    addShop() {
      this.editTitle = '新增';
      this.editType = 'post';
      this.form = {};
      this.editDialog = true;
    },

    putShop(shop) {
      delete shop.changeRecord;
      delete shop.__v;
      axios.put(`${this.APIHOST}/api/shop`, shop)
        .then((response) => {
          this.editDialog = false;
          this.getShopList();
        });
    },

    postShop(shop) {
      delete shop.changeRecord;
      delete shop.__v;
      delete shop._id;
      axios.post(`${this.APIHOST}/api/shop`, shop)
        .then((response) => {
          this.editDialog = false;
          this.getShopList();
        });
    },

    getShopList() {
      axios.get(`${this.APIHOST}/api/shop`)
        .then((response) => {
          this.list = response.data;
        });
    },

    getTagList() {
      axios.get(`${this.APIHOST}/api/tag`)
        .then((response) => {
          this.tagList = response.data;
        });
    },

    getTagName(_id) {
      const tags = this.tagList.filter((one)=>one._id === _id);
      if (tags.length > 0) {
        return tags[0].name;
      } else {
        return '標籤已被刪除';
      }
    },

    submitForm() {
      this.$refs['form'].validate((valid) => {
        if (valid) {
          if (this.editType === 'post') {
            this.postShop(this.form);
          }
          if (this.editType === 'put') {
            this.putShop(this.form);
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
