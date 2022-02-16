<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('考试题库')">导出</a-button>
      <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>

      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical" />
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <cms-examination-modal ref="modalForm" @ok="modalFormOk"></cms-examination-modal>
  </a-card>
</template>

<script>

  import '@assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import CmsExaminationModal from './modules/CmsExaminationModal'

  export default {
    name: 'CmsExaminationList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      CmsExaminationModal
    },
    data () {
      return {
        description: '考试题库管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'视频编号ID',
            align:"center",
            dataIndex: 'courseCode'
          },
          {
            title:'题目类型（多选2 单选1）',
            align:"center",
            dataIndex: 'topicType'
          },
          {
            title:'题目标题',
            align:"center",
            dataIndex: 'questionText'
          },
          {
            title:'题目正确答案',
            align:"center",
            dataIndex: 'correctAnswer'
          },
          {
            title:'选项A',
            align:"center",
            dataIndex: 'answerForA'
          },
          {
            title:'选项B',
            align:"center",
            dataIndex: 'answerForB'
          },
          {
            title:'选项C',
            align:"center",
            dataIndex: 'answerForC'
          },
          {
            title:'选项D',
            align:"center",
            dataIndex: 'answerForD'
          },
          {
            title:'选项分析',
            align:"center",
            dataIndex: 'answerRemark'
          },
          {
            title:'对应排列序号',
            align:"center",
            dataIndex: 'fillNumber'
          },
          {
            title:'删除标志 0未删除 1已删除',
            align:"center",
            dataIndex: 'delFlag'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/cms/manager/cmsExamination/list",
          delete: "/cms/manager/cmsExamination/delete",
          deleteBatch: "/cms/manager/cmsExamination/deleteBatch",
          exportXlsUrl: "/cms/manager/cmsExamination/exportXls",
          importExcelUrl: "/cms/manager/cmsExamination/importExcel",

        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'int',value:'courseCode',text:'视频编号ID',dictCode:''})
        fieldList.push({type:'int',value:'topicType',text:'题目类型（多选2 单选1）',dictCode:''})
        fieldList.push({type:'string',value:'questionText',text:'题目标题',dictCode:''})
        fieldList.push({type:'string',value:'correctAnswer',text:'题目正确答案',dictCode:''})
        fieldList.push({type:'string',value:'answerForA',text:'选项A',dictCode:''})
        fieldList.push({type:'string',value:'answerForB',text:'选项B',dictCode:''})
        fieldList.push({type:'string',value:'answerForC',text:'选项C',dictCode:''})
        fieldList.push({type:'string',value:'answerForD',text:'选项D',dictCode:''})
        fieldList.push({type:'string',value:'answerRemark',text:'选项分析',dictCode:''})
        fieldList.push({type:'string',value:'fillNumber',text:'对应排列序号',dictCode:''})
        fieldList.push({type:'int',value:'delFlag',text:'删除标志 0未删除 1已删除',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>
