<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="小程序昵称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="appletNickName">-->
<!--              <a-input v-model="model.appletNickName" placeholder="请输入小程序昵称"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="小程序头像" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="avatarUrl">-->
<!--              <j-upload v-model="model.avatarUrl"   ></j-upload>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="unionid小程序和公众号关联字段" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="unionid">-->
<!--              <a-input v-model="model.unionid" placeholder="请输入unionid小程序和公众号关联字段"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="小程序openid" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="appletOpenid">-->
<!--              <a-input v-model="model.appletOpenid" placeholder="请输入小程序openid"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="小程序session_key" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="sessionKey">-->
<!--              <a-input v-model="model.sessionKey" placeholder="请输入小程序session_key"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="小程序手机号码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="miniTel">-->
<!--              <a-input v-model="model.miniTel" placeholder="请输入小程序手机号码"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
          <a-col :span="24">
            <a-form-model-item label="公众号昵称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="nickNameMphelper">
              <a-input v-model="model.nickNameMphelper" placeholder="请输入公众号昵称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="公众号头像" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="headimgurl">
              <j-image-upload isMultiple  v-model="model.headimgurl" ></j-image-upload>
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="公众号openid" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="openidMphelper">-->
<!--              <a-input v-model="model.openidMphelper" placeholder="请输入公众号openid"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
          <a-col :span="24">
            <a-form-model-item label="真实姓名" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="realName">
              <a-input v-model="model.realName" placeholder="请输入真实姓名"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="手机号码" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tel">
              <a-input v-model="model.tel" placeholder="请输入手机号码"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="公司名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="companyName">
              <a-input v-model="model.companyName" placeholder="请输入公司名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="公司职位" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="companyPosition">
              <a-input v-model="model.companyPosition" placeholder="请输入公司职位"  ></a-input>
            </a-form-model-item>
          </a-col>

          <a-col :span="24">
            <a-form-model-item label="证件照" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="identificationPhoto">
              <j-image-upload isMultiple  v-model="model.identificationPhoto" ></j-image-upload>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="公司税务号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="companyNo">
              <a-input v-model="model.companyNo" placeholder="请输入公司税务号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="资料更新" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="status">
              <j-dict-select-tag type="radio" v-model="model.status" dictCode="imUser_status" placeholder="请选择资料更新" />
            </a-form-model-item>
          </a-col>
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="删除标识" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="delFlag">-->
<!--              <j-dict-select-tag type="radio" v-model="model.delFlag" dictCode="del_flag" placeholder="请选择删除标识" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="创建时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="creTime">-->
<!--              <j-date placeholder="请选择创建时间" v-model="model.creTime"  style="width: 100%" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="更新时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="updTime">-->
<!--              <j-date placeholder="请选择更新时间" v-model="model.updTime"  style="width: 100%" />-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="token" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="token">-->
<!--              <a-input v-model="model.token" placeholder="请输入token"  ></a-input>-->
<!--            </a-form-model-item>-->
<!--          </a-col>-->
<!--          <a-col :span="24">-->
<!--            <a-form-model-item label="token过期时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="tokenTime">-->
<!--              <j-date placeholder="请选择token过期时间" v-model="model.tokenTime"  style="width: 100%" />-->
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
    name: 'ImUserForm',
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
           realName: [
              { required: true, message: '请输入真实姓名!'},
           ],
           tel: [
              { required: false},
              { pattern: /^1[3456789]\d{9}$/, message: '请输入正确的手机号码!'},
           ],
           companyName: [
              { required: true, message: '请输入公司名称!'},
           ],
           companyPosition: [
              { required: true, message: '请输入公司职位!'},
           ],
           identificationPhoto: [
              { required: true, message: '请输入证件照!'},
           ],
           companyNo: [
              { required: true, message: '请输入公司税务号!'},
           ],
        },
        url: {
          add: "/im/manager/imUser/add",
          edit: "/im/manager/imUser/edit",
          queryById: "/im/manager/imUser/queryById"
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
