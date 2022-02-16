<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="视频编号ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="videoCode">
              <a-input-number v-model="model.videoCode" placeholder="请输入视频编号ID" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目类型" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="topicType">
              <j-dict-select-tag type="radio" v-model="model.topicType" dictCode="topic_type" placeholder="请选择题目类型" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="排序" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="fillNumber">
              <a-input v-model="model.fillNumber" placeholder="请输入排序"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目标题" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="topicHeadline">
              <a-input-number v-model="model.topicHeadline" placeholder="请输入题目标题" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="正确答案" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="topicAnswer">
              <j-multi-select-tag type="checkbox" v-model="model.topicAnswer" dictCode="answer_for" placeholder="请选择正确答案" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="选项A" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerForA">
              <a-input v-model="model.answerForA" placeholder="请输入选项A"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="选项B" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerForB">
              <a-input v-model="model.answerForB" placeholder="请输入选项B"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="选项C" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerForC">
              <a-input v-model="model.answerForC" placeholder="请输入选项C"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="选项D" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerForD">
              <a-input v-model="model.answerForD" placeholder="请输入选项D"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目解析" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerRemark">
              <j-editor v-model="model.answerRemark" />
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
    name: 'CmsTopicForm',
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
           videoCode: [
              { required: true, message: '请输入视频编号ID!'},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
           topicType: [
              { required: true, message: '请输入题目类型!'},
           ],
           fillNumber: [
              { required: true, message: '请输入排序!'},
              { pattern: /^-?\d+\.?\d*$/, message: '请输入数字!'},
           ],
           topicHeadline: [
              { required: true, message: '请输入题目标题!'},
           ],
           topicAnswer: [
              { required: true, message: '请输入正确答案!'},
           ],
        },
        url: {
          add: "/cms/manager/cmsTopic/add",
          edit: "/cms/manager/cmsTopic/edit",
          queryById: "/cms/manager/cmsTopic/queryById"
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