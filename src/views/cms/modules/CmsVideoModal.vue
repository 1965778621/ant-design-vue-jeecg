<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    switchFullscreen
    @ok="handleOk"
    :okButtonProps="{ class:{'jee-hidden': disableSubmit} }"
    @cancel="handleCancel"
    cancelText="关闭">
    <cms-video-form ref="realForm" @ok="submitCallback" :disabled="disableSubmit"></cms-video-form>
  </j-modal>
</template>

<script>

import CmsVideoForm from './CmsVideoForm'
export default {
  name: 'CmsVideoModal',
  components: {
    CmsVideoForm
  },
  data () {
    return {
      title:'',
      width:800,
      visible: false,
      disableSubmit: false
    }
  },
  methods: {
    add () {
      this.visible=true
      this.$nextTick(()=>{
        this.$refs.realForm.add();
      })
    },
    edit (record) {
      this.visible=true
      this.$nextTick(()=>{
        this.$refs.realForm.edit(record);
      })
    },
    close () {
      this.$emit('close');
      this.visible = false;
    },
    handleOk () {
      this.$refs.realForm.submitForm();
    },
    submitCallback(){
      this.$emit('ok');
      this.visible = false;
    },
    handleCancel () {
      this.close()
    }
  }
}
</script>