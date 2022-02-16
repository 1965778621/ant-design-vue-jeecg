<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="参数键名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="cmsKey">
              <a-input v-model="model.cmsKey" placeholder="请输入参数键名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="参数名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="name">
              <a-input v-model="model.name" placeholder="请输入参数名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="参数键值" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="value">
              <a-input v-model="model.value" placeholder="请输入参数键值"  ></a-input>
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="系统内置" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="type">-->
<!--              <j-dict-select-tag type="radio" v-model="model.type" dictCode="config_type" placeholder="请选择系统内置" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
          <a-col :span="24">
            <a-form-model-item label="备注" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="remark">
              <a-input v-model="model.remark" placeholder="请输入备注"  ></a-input>
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
    name: 'CmsConfigForm',
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
          cmsKey: [
              { required: true, message: '请输入参数键名!'},
              { validator: (rule, value, callback) => validateDuplicateValue('cms_config', 'cms_key', value, this.model.id, callback)},
           ],
           name: [
              { required: true, message: '请输入参数名称!'},
           ],
           value: [
              { required: true, message: '请输入参数键值!'},
           ],
           type: [
              { required: true, message: '请输入系统内置!'},
           ],
           delFlag: [
              { required: true, message: '请输入删除标志!'},
           ],
        },
        url: {
          add: "/cms/manager/cmsConfig/add",
          edit: "/cms/manager/cmsConfig/edit",
          queryById: "/cms/manager/cmsConfig/queryById"
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