<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="视频编号ID" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="courseCode">
              <a-input-number v-model="model.courseCode" placeholder="请输入视频编号ID" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目类型（多选2 单选1）" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="topicType">
              <a-input-number v-model="model.topicType" placeholder="请输入题目类型（多选2 单选1）" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目标题" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="questionText">
              <a-input v-model="model.questionText" placeholder="请输入题目标题"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="题目正确答案" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="correctAnswer">
              <a-input v-model="model.correctAnswer" placeholder="请输入题目正确答案"  ></a-input>
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
            <a-form-model-item label="选项分析" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="answerRemark">
              <a-input v-model="model.answerRemark" placeholder="请输入选项分析"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="对应排列序号" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="fillNumber">
              <a-input v-model="model.fillNumber" placeholder="请输入对应排列序号"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="删除标志 0未删除 1已删除" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="delFlag">
              <a-input-number v-model="model.delFlag" placeholder="请输入删除标志 0未删除 1已删除" style="width: 100%" />
            </a-form-model-item>
          </a-col>
        </a-row>
      </a-form-model>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@api/manage'
  import { validateDuplicateValue } from '@/utils/util'

  export default {
    name: 'CmsExaminationForm',
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
        },
        url: {
          add: "/cms/manager/cmsExamination/add",
          edit: "/cms/manager/cmsExamination/edit",
          queryById: "/cms/manager/cmsExamination/queryById"
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
