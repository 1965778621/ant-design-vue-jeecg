<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="标题" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="title">
              <a-input v-model="model.title" placeholder="请输入标题"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="封面" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="img">
              <j-image-upload isMultiple  v-model="model.img" ></j-image-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="视频超链接" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="urldetail">
              <j-upload v-model="model.urldetail"   ></j-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="url">
              <a-input v-model="model.url" placeholder="请输入地址"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="时长(单位秒)" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="time">
              <a-input-number v-model="model.time" placeholder="请输入时长(单位秒)" style="width: 100%" />
            </a-form-model-item>
          </a-col>

<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="删除标志" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="delFlag">-->
<!--              <j-dict-select-tag type="radio" v-model="model.delFlag" dictCode="del_flag" placeholder="请选择删除标志" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="是否跳转" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tzType">-->
<!--              <j-dict-select-tag type="radio" v-model="model.tzType" dictCode="video_type" placeholder="请选择是否跳转" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          {{model.tzType}}-->
          <a-col :span="24">
            <a-form-model-item label="跳转的url" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tzUrl">
              <j-editor v-model="model.tzUrl" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

import { httpAction, getAction } from '@/api/manage'
import { validateDuplicateValue } from '@/utils/util'

export default {
  name: 'CmsVideoForm',
  components: {
  },
  props: {
    //表单禁用
    disabled: {
      type: Boolean,
      default: false,
      required: false
    }
  },
  data () {
    return {
      model:{
      },
      labelCol: {
        xs: { span: 24 },
        sm: { span: 5 },
      },
      wrapperCol: {
        xs: { span: 24 },
        sm: { span: 16 },
      },
      confirmLoading: false,
      validatorRules: {
        title: [
          { required: true, message: '请输入标题!'},
        ],
        time: [
          { required: false},
          { pattern: /^-?\d+$/, message: '请输入整数!'},
        ],
        url: [
          { required: true, message: '请输入地址!'},
        ],
        urldetail: [
          { required: true, message: '请输入视频超链接!'},
        ],
      },
      url: {
        add: "/cms/manager//cmsVideo/add",
        edit: "/cms/manager//cmsVideo/edit",
        queryById: "/cms/manager//cmsVideo/queryById"
      }
    }
  },
  computed: {
    formDisabled(){
      return this.disabled
    },
  },
  created () {
    //备份model原始值
    this.modelDefault = JSON.parse(JSON.stringify(this.model));
  },
  methods: {
    add () {
      this.edit(this.modelDefault);
    },
    edit (record) {
      this.model = Object.assign({}, record);
      this.visible = true;
    },
    submitForm () {
      const that = this;
      // 触发表单验证
      this.$refs.form.validate(valid => {
        if (valid) {
          that.confirmLoading = true;
          let httpurl = '';
          let method = '';
          if(!this.model.id){
            httpurl+=this.url.add;
            method = 'post';
          }else{
            httpurl+=this.url.edit;
            method = 'put';
          }
          httpAction(httpurl,this.model,method).then((res)=>{
            if(res.success){
              that.$message.success(res.message);
              that.$emit('ok');
            }else{
              that.$message.warning(res.message);
            }
          }).finally(() => {
            that.confirmLoading = false;
          })
        }

      })
    },
  }
}
</script>
