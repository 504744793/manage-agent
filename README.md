DataSyncConfig.vue 文件
<template>
  <a-modal v-model:visible="visible" title="数据同步配置" @ok="handleOk" @cancel="handleCancel" width="1000px">
    <a-form :model="formState" :label-col="{ span: 6 }" :wrapper-col="{ span: 18 }">
      <!-- 前面的表单内容保持不变 -->
      <a-row :gutter="[24, 24]">
        <a-col :span="12">
          <a-form-item label="源数据库">
            <a-input value="源数据库" disabled />
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item label="目标数据库">
            <a-input value="目标数据库" disabled />
          </a-form-item>
        </a-col>
      </a-row>

      <a-row :gutter="[24, 24]">
        <a-col :span="12">
          <a-form-item label="装载方式">
            <a-select v-model:value="formState.loadMethod" placeholder="请选择装载方式">
              <a-select-option value="option1">选项一</a-select-option>
              <a-select-option value="option2">选项二</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item label="源表的数据源">
            <a-select v-model:value="formState.sourceDataSource" placeholder="请选择源表的数据源">
              <a-select-option value="source1">源数据源1</a-select-option>
              <a-select-option value="source2">源数据源2</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row :gutter="[24, 24]">
        <a-col :span="12">
          <a-form-item label="源表">
            <a-select v-model:value="formState.sourceTable" placeholder="请选择源表">
              <a-select-option value="table1">表1</a-select-option>
              <a-select-option value="table2">表2</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
        <a-col :span="12">
          <a-form-item label="目标表的数据源">
            <a-select v-model:value="formState.targetDataSource" placeholder="请选择目标表的数据源">
              <a-select-option value="target1">目标数据源1</a-select-option>
              <a-select-option value="target2">目标数据源2</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>

      <a-row :gutter="[24, 24]">
        <a-col :span="12">
          <a-form-item label="目标表">
            <a-select v-model:value="formState.targetTable" placeholder="请选择目标表">
              <a-select-option value="table1">表1</a-select-option>
              <a-select-option value="table2">表2</a-select-option>
            </a-select>
          </a-form-item>
        </a-col>
      </a-row>

      <!-- 字段映射配置项表格 -->
      <template v-if="formState.loadMethod === 'option2'">
        <a-button type="primary" style="margin-bottom: 16px" @click="handleAdd">
          <plus-outlined /> 新增字段映射
        </a-button>

        <!-- 使用 OutputClmnTable 替换原有的 a-table -->
        <OutputClmnTable
          :columns="columns"
          :dataSource="tableData"
          :pagination="false"
          bordered
          :scroll="{ x: '100%' }"
          row-key="key"
          @cell-change="handleFieldChange"
          @delete-row="handleDelete"
        />
      </template>
    </a-form>
  </a-modal>
</template>

<script lang="ts">
import { defineComponent, reactive, toRefs } from 'vue';
import { message } from 'ant-design-vue';
import { PlusOutlined } from '@ant-design/icons-vue';
import OutputClmnTable from './OutputClmnTable2.vue';

interface TableItem {
  key: number;
  sourceColumn: string;
  sourceColumnCn: string;
  sourceColumnType: string;
  sourceColumnLength: string;
  sourceColumnPrecision: string;
  isPrimaryKey: string;
  targetColumn: string;
  targetColumnCn: string;
  targetColumnType: string;
  targetColumnLength: string;
  targetColumnPrecision: string;
  [key: string]: any;
}

export default defineComponent({
  name: 'DataSyncConfig',
  components: {
    PlusOutlined,
    OutputClmnTable,
  },
  setup() {
    const state = reactive({
      visible: false,
      formState: {
        loadMethod: '',
        sourceDataSource: '',
        sourceTable: '',
        targetDataSource: '',
        targetTable: '',
      },
      tableData: [] as TableItem[],
      columns: [
        {
          title: '序号',
          dataIndex: 'index',
          width: 60,
          align: 'center',
        },
        {
          title: '源表字段',
          dataIndex: 'sourceColumn',
          width: 120,
          editable: true,
          placeholder: '请输入源表字段',
        },
        {
          title: '源表字段中文名',
          dataIndex: 'sourceColumnCn',
          width: 150,
          editable: true,
          placeholder: '请输入源表字段中文名',
        },
        {
          title: '源表字段类型',
          dataIndex: 'sourceColumnType',
          width: 120,
          type: 'select',
          editable: true,
          options: [
            { value: 'VARCHAR', label: 'VARCHAR' },
            { value: 'INTEGER', label: 'INTEGER' },
            { value: 'DECIMAL', label: 'DECIMAL' },
            { value: 'DATETIME', label: 'DATETIME' },
          ],
        },
        {
          title: '源表字段长度',
          dataIndex: 'sourceColumnLength',
          width: 120,
          editable: true,
          placeholder: '请输入字段长度',
        },
        {
          title: '源表字段精度',
          dataIndex: 'sourceColumnPrecision',
          width: 120,
          editable: true,
          placeholder: '请输入字段精度',
        },
        {
          title: '是否主键',
          dataIndex: 'isPrimaryKey',
          width: 100,
          type: 'select',
          editable: true,
          options: [
            { value: '是', label: '是' },
            { value: '否', label: '否' },
          ],
        },
        {
          title: '目标表字段',
          dataIndex: 'targetColumn',
          width: 120,
          editable: true,
          placeholder: '请输入目标表字段',
        },
        {
          title: '目标表字段中文名',
          dataIndex: 'targetColumnCn',
          width: 150,
          editable: true,
          placeholder: '请输入目标表字段中文名',
        },
        {
          title: '目标表字段类型',
          dataIndex: 'targetColumnType',
          width: 120,
          type: 'select',
          editable: true,
          options: [
            { value: 'VARCHAR', label: 'VARCHAR' },
            { value: 'INTEGER', label: 'INTEGER' },
            { value: 'DECIMAL', label: 'DECIMAL' },
            { value: 'DATETIME', label: 'DATETIME' },
          ],
        },
        {
          title: '目标表字段长度',
          dataIndex: 'targetColumnLength',
          width: 120,
          editable: true,
          placeholder: '请输入字段长度',
        },
        {
          title: '目标表字段精度',
          dataIndex: 'targetColumnPrecision',
          width: 120,
          editable: true,
          placeholder: '请输入字段精度',
        },
        {
          title: '操作',
          dataIndex: 'operation',
          width: 80,
          align: 'center',
        },
      ],
    });

    // 当单元格的值发生变化时调用该方法
    const handleFieldChange = (record: TableItem, dataIndex: string, value: any) => {
      const index = state.tableData.findIndex(item => item.key === record.key);
      if (index > -1) {
        // 使用 Object.assign 来更新对象
        Object.assign(state.tableData[index], { [dataIndex]: value });
      }
    };

    // 新增一行字段映射
    const handleAdd = () => {
      const newRow: TableItem = {
        key: Date.now(), // 使用时间戳作为唯一 key
        sourceColumn: '',
        sourceColumnCn: '',
        sourceColumnType: 'VARCHAR',
        sourceColumnLength: '',
        sourceColumnPrecision: '',
        isPrimaryKey: '否',
        targetColumn: '',
        targetColumnCn: '',
        targetColumnType: 'VARCHAR',
        targetColumnLength: '',
        targetColumnPrecision: '',
      };
      state.tableData = [...state.tableData, newRow];
    };

    // 删除行操作
    const handleDelete = (key: number) => {
      state.tableData = state.tableData.filter(item => item.key !== key);
      message.success('删除成功');
    };

    const handleOk = () => {
      if (!state.formState.loadMethod) {
        message.error('请选择装载方式');
        return;
      }
      if (state.formState.loadMethod === 'option2' && state.tableData.length === 0) {
        message.error('请添加字段映射');
        return;
      }
      console.log('Form State:', state.formState);
      console.log('Table Data:', state.tableData);
      state.visible = false;
    };

    const handleCancel = () => {
      state.visible = false;
    };

    const showModal = () => {
      state.visible = true;
    };

    return {
      ...toRefs(state),
      handleAdd,
      handleFieldChange,
      showModal,
      handleOk,
      handleCancel,
      handleDelete,
    };
  },
});
</script>

<style scoped>
/* 可在此处补充组件的局部样式 */
</style>




OutputClmnTable2.vue 文件
<template>
  <a-table
    :columns="internalColumns"
    :data-source="dataSource"
    :pagination="pagination"
    :bordered="bordered"
    :scroll="scroll"
    :rowKey="rowKey"
  >
    <template #bodyCell="{ column, record, index }">
      <template v-if="column.dataIndex === 'index'">
        {{ index + 1 }}
      </template>
      <template v-else-if="column.dataIndex === 'operation'">
        <a-button type="link" @click="onEdit(record)">编辑</a-button>
        <a-button type="link" danger @click="onDelete(record.key)">删除</a-button>
      </template>
      <template v-else>
        {{ record[column.dataIndex] }}
      </template>
    </template>
  </a-table>
  <!-- 编辑模态框 -->
  <a-modal v-model:visible="editModalVisible" title="编辑数据" @ok="handleSave" @cancel="handleCancel">
    <a-form :model="editingRecord" :label-col="labelCol" :wrapper-col="wrapperCol">
      <a-form-item v-for="col in editableColumns" :key="col.dataIndex" :label="col.title" :name="col.dataIndex">
        <a-select
          v-if="col.type === 'select'"
          v-model:value="editingRecord[col.dataIndex]"
          style="width: 100%"
          size="small"
        >
          <a-select-option v-for="option in col.options" :key="option.value" :value="option.value">
            {{ option.label }}
          </a-select-option>
        </a-select>
        <a-input v-else v-model:value="editingRecord[col.dataIndex]" size="small" />
      </a-form-item>
    </a-form>
  </a-modal>
</template>

<script lang="ts">
import { defineComponent, ref, computed, PropType } from 'vue';
import { message } from 'ant-design-vue';

interface ColumnProps {
  title: string;
  dataIndex: string;
  width?: number;
  align?: string;
  editable?: boolean;
  type?: 'text' | 'select';
  options?: { value: string; label: string }[];
  [key: string]: any;
}

interface TableItem {
  key: number;
  [key: string]: any;
}

export default defineComponent({
  name: 'OutputClmnTable',
  props: {
    columns: {
      type: Array as PropType<ColumnProps[]>,
      required: true,
    },
    dataSource: {
      type: Array as PropType<TableItem[]>,
      required: true,
    },
    pagination: {
      type: [Object, Boolean],
      default: false,
    },
    bordered: {
      type: Boolean,
      default: false,
    },
    scroll: {
      type: Object,
      default: () => ({}),
    },
    rowKey: {
      type: String,
      required: true,
    },
  },
  emits: ['cell-change', 'delete-row'],
  setup(props, { emit }) {
    const editModalVisible = ref(false);
    const editingRecord = ref<TableItem>({ key: -1 });
    const labelCol = { span: 6 };
    const wrapperCol = { span: 18 };

    const internalColumns = computed(() => {
      return props.columns.map((col) => {
        if (col.dataIndex === 'index') {
          return {
            ...col,
            customRender: ({ index }: { index: number }) => index + 1,
          };
        }
        return col;
      });
    });

    const editableColumns = computed(() => {
      return props.columns.filter((col) => col.editable);
    });

    const onEdit = (record: TableItem) => {
      editingRecord.value = { ...record };
      editModalVisible.value = true;
    };

    const handleSave = () => {
      emit('cell-change', editingRecord.value, null, editingRecord.value);
      editModalVisible.value = false;
    };

    const handleCancel = () => {
      editModalVisible.value = false;
    };

    const onDelete = (key: number) => {
      emit('delete-row', key);
    };

    return {
      internalColumns,
      editingRecord,
      editModalVisible,
      labelCol,
      wrapperCol,
      editableColumns,
      onEdit,
      handleSave,
      handleCancel,
      onDelete,
    };
  },
});
</script>

<style scoped>
/* 样式保持不变 */
</style>
