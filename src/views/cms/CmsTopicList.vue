<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="视频编号ID">
              <a-input placeholder="请输入视频编号ID" v-model="queryParam.videoCode"></a-input>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="题目类型">
              <j-dict-select-tag placeholder="请选择题目类型" v-model="queryParam.topicType" dictCode="topic_type"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="题目标题">
                <a-input placeholder="请输入题目标题" v-model="queryParam.topicHeadline"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="正确答案">
                <j-dict-select-tag placeholder="请选择正确答案" v-model="queryParam.topicAnswer" dictCode="answer_for"/>
              </a-form-item>
            </a-col>
          </template>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button type="primary" icon="download" @click="handleExportXls('考试题目表')">导出</a-button>
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

    <cms-topic-modal ref="modalForm" @ok="modalFormOk"></cms-topic-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import CmsTopicModal from './modules/CmsTopicModal'
  import {filterMultiDictText} from '@/components/dict/JDictSelectUtil'

  export default {
    name: 'CmsTopicList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      CmsTopicModal
    },
    data () {
      return {
        description: '考试题目表管理页面',
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
            dataIndex: 'videoCode'
          },
          {
            title:'题目类型',
            align:"center",
            dataIndex: 'topicType_dictText'
          },
          {
            title:'排序',
            align:"center",
            dataIndex: 'fillNumber'
          },
          {
            title:'题目标题',
            align:"center",
            dataIndex: 'topicHeadline'
          },
          {
            title:'正确答案',
            align:"center",
            dataIndex: 'topicAnswer_dictText'
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
            title:'删除标志',
            align:"center",
            dataIndex: 'delFlag_dictText'
          },
          {
            title:'创建时间',
            align:"center",
            dataIndex: 'createTime',
            customRender:function (text) {
              return !text?"":(text.length>10?text.substr(0,10):text)
            }
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
          list: "/cms/manager/cmsTopic/list",
          delete: "/cms/manager/cmsTopic/delete",
          deleteBatch: "/cms/manager/cmsTopic/deleteBatch",
          exportXlsUrl: "/cms/manager/cmsTopic/exportXls",
          importExcelUrl: "/cms/manager/cmsTopic/importExcel",

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
        fieldList.push({type:'int',value:'videoCode',text:'视频编号ID',dictCode:''})
        fieldList.push({type:'string',value:'topicType',text:'题目类型',dictCode:'topic_type'})
        fieldList.push({type:'string',value:'fillNumber',text:'排序',dictCode:''})
        fieldList.push({type:'int',value:'topicHeadline',text:'题目标题',dictCode:''})
        fieldList.push({type:'string',value:'topicAnswer',text:'正确答案',dictCode:'answer_for'})
        fieldList.push({type:'string',value:'answerForA',text:'选项A',dictCode:''})
        fieldList.push({type:'string',value:'answerForB',text:'选项B',dictCode:''})
        fieldList.push({type:'string',value:'answerForC',text:'选项C',dictCode:''})
        fieldList.push({type:'string',value:'answerForD',text:'选项D',dictCode:''})
        fieldList.push({type:'string',value:'answerRemark',text:'题目解析',dictCode:''})
        fieldList.push({type:'int',value:'delFlag',text:'删除标志',dictCode:'del_flag'})
        fieldList.push({type:'date',value:'createTime',text:'创建时间'})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>