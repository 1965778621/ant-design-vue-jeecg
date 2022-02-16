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
            <a-form-model-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sort">
              <a-input-number v-model="model.sort" placeholder="请输入排序" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="图片地址" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="img">
              <j-image-upload isMultiple  v-model="model.img" ></j-image-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="跳转的url" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="url">
              <j-editor v-model="model.url" />
            </a-form-model-item>
          </a-col>

<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="轮播图编组" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="series">-->
<!--              <a-input v-model="model.series" placeholder="请输入轮播图编组"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="type">-->
<!--              <j-dict-select-tag type="radio" v-model="model.type" dictCode="" placeholder="请选择类型" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->

<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="参数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="parm">-->
<!--              <a-input v-model="model.parm" placeholder="请输入参数"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->


<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="删除标志" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="delFlag">-->
<!--              <j-dict-select-tag type="list" v-model="model.delFlag" dictCode="" placeholder="请选择删除标志" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'CmsBannerForm',
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
          sort: [
            { required: true, message: '请输入参数键名!'},
          ],
          title: [
            { required: true, message: '请输入参数名称!'},
          ],
        },
        url: {
          add: "/cms/manager/cmsBanner/add",
          edit: "/cms/manager/cmsBanner/edit",
          queryById: "/cms/manager/cmsBanner/queryById"
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