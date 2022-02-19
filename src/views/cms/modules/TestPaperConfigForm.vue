<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form-model ref="form" :model="model" :rules="validatorRules" slot="detail">
        <a-row>
          <a-col :span="24">
            <a-form-model-item label="试卷名称" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="testName">
              <a-input v-model="model.testName" placeholder="请输入试卷名称"  ></a-input>
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="考试时间" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="testTime">
              <a-input-number v-model="model.testTime" placeholder="请输入考试时间" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="判断题数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="judgeTopicNum">
              <a-input-number v-model="model.judgeTopicNum" placeholder="请输入判断题数量" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="判断题每题分数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="judgeTopicScore">
              <a-input-number v-model="model.judgeTopicScore" placeholder="请输入判断题每题分数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="单选题题数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="singleChoiceNum">
              <a-input-number v-model="model.singleChoiceNum" placeholder="请输入单选题题数量" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="单选题每题分数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="singleChoiceScore">
              <a-input-number v-model="model.singleChoiceScore" placeholder="请输入单选题每题分数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="多选题数量" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="multipleChoiceNum">
              <a-input-number v-model="model.multipleChoiceNum" placeholder="请输入多选题数量" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="多选题每题分数" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="multipleChoiceScore">
              <a-input-number v-model="model.multipleChoiceScore" placeholder="请输入多选题每题分数" style="width: 100%" />
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="总分" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="totalPoints">
              <a-input-number v-model="model.totalPoints" :placeholder="(model.judgeTopicNum*model.judgeTopicScore)" style="width: 100%" />
            {{model.totalPoints}}
            </a-form-model-item>
          </a-col>
          <a-col :span="24">
            <a-form-model-item label="试卷描述" :labelCol="labelCol" :wrapperCol="wrapperCol" prop="testDescribe">
              <a-input v-model="model.testDescribe" placeholder="请输入试卷描述"  ></a-input>
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
    name: 'TestPaperConfigForm',
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
           testName: [
              { required: true, message: '请输入试卷名称!'},
           ],
           testTime: [
              { required: true, message: '请输入考试时间!'},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           judgeTopicNum: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           judgeTopicScore: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           singleChoiceNum: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           singleChoiceScore: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           multipleChoiceNum: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
           multipleChoiceScore: [
              { required: false},
              { pattern: /^-?\d+$/, message: '请输入整数!'},
           ],
        },
        url: {
          add: "/cms/manager/testPaperConfig/add",
          edit: "/cms/manager/testPaperConfig/edit",
          queryById: "/cms/manager/testPaperConfig/queryById"
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
