<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="用户编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="userCode">
              <a-input v-model="model.userCode" placeholder="请输入用户编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="视频编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="videoCode">
              <a-input-number v-model="model.videoCode" placeholder="请输入视频编号" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目编号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="topicCode">
              <a-input v-model="model.topicCode" placeholder="请输入题目编号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="用户选择" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="userSelection">
              <j-multi-select-tag type="checkbox" v-model="model.userSelection" dictCode="answer_for" placeholder="请选择用户选择" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="createTime">
              <j-date placeholder="请选择创建时间" v-model="model.createTime"  style="width: 100%" />
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
    name: 'CmsExamRecordForm',
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
           userCode: [
              { required: true, message: '请输入用户编号!'},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
           videoCode: [
              { required: true, message: '请输入视频编号!'},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
           topicCode: [
              { required: true, message: '请输入题目编号!'},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
        },
        url: {
          add: "/cms/manager/cmsExamRecord/add",
          edit: "/cms/manager/cmsExamRecord/edit",
          queryById: "/cms/manager/cmsExamRecord/queryById"
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