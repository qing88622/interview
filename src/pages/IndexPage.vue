<template>
  <q-page class="row q-pt-xl">
    <div class="full-width q-px-xl">
      <div class="q-mb-xl">
        <q-input v-model="tempData.name" label="姓名" />
        <q-input v-model="tempData.age" label="年齡" type="number"/>
        <q-btn color="primary" class="q-mt-md" @click="handleSubmit">{{ isEdit?'儲存編輯':'新增' }}</q-btn>
      </div>
      {{ isEdit }}

      <q-table
        flat
        bordered
        ref="tableRef"
        :rows="blockData"
        :columns="(tableConfig as QTableProps['columns'])"
        row-key="id"
        hide-pagination
        separator="cell"
        :rows-per-page-options="[0]"
      >
        <template v-slot:header="props">
          <q-tr :props="props">
            <q-th v-for="col in props.cols" :key="col.name" :props="props">
              {{ col.label }}
            </q-th>
            <q-th></q-th>
          </q-tr>
        </template>

        <template v-slot:body="props">
          <q-tr :props="props">
            <q-td
              v-for="col in props.cols"
              :key="col.name"
              :props="props"
              style="min-width: 120px"
            >
              <div>{{ col.value }}</div>
            </q-td>
            <q-td class="text-right" auto-width v-if="tableButtons.length > 0">
              <q-btn
                @click="handleClickOption(btn, props.row)"
                v-for="(btn, index) in tableButtons"
                :key="index"
                size="sm"
                color="grey-6"
                round
                dense
                :icon="btn.icon"
                class="q-ml-md"
                padding="5px 5px"
              >
                <q-tooltip
                  transition-show="scale"
                  transition-hide="scale"
                  anchor="top middle"
                  self="bottom middle"
                  :offset="[10, 10]"
                >
                  {{ btn.label }}
                </q-tooltip>
              </q-btn>
            </q-td>
          </q-tr>
        </template>
        <template v-slot:no-data="{ icon }">
          <div
            class="full-width row flex-center items-center text-primary q-gutter-sm"
            style="font-size: 18px"
          >
            <q-icon size="2em" :name="icon" />
            <span> 無相關資料 </span>
          </div>
        </template>
      </q-table>
    </div>
  </q-page>
</template>

<script setup lang="ts">
import axios from 'axios';
import { QTableProps } from 'quasar';
import { ref } from 'vue';
// const testApi = ref("data")
// const fetchData = async () => {
//   try {
//     const response = await axios.get('/crudTest'); 
//     testApi.value = response.data; 
//     // error.value = null; 
//   } catch (err) {
//     console.error(err)
//   }
// };

// fetchData()
let isEdit =ref(false)
interface btnType {
  label: string;
  icon: string;
  status: string;
}

const blockData = ref([
  {
    id: 1,
    name: 'test',
    age: 25,
  },
]);

const tableConfig = ref([
  {
    label: '姓名',
    name: 'name',
    field: 'name',
    align: 'left',
  },
  {
    label: '年齡',
    name: 'age',
    field: 'age',
    align: 'left',
  },
]);

const tableButtons = ref([
  {
    label: '編輯',
    icon: 'edit',
    status: 'edit',
  },
  {
    label: '刪除',
    icon: 'delete',
    status: 'delete',
  },
]);

const tempData = ref({
  name: '',
  age: '',
});

function generateUniqueId() {
  return Date.now() + '-' + Math.floor(Math.random() * 10000);
}

function handleClickOption(btn, data) {
  if (btn.status === 'edit') {
    isEdit.value=true
    tempData.value = JSON.parse(JSON.stringify(data))
  } else if (btn.status === 'delete') {
    const index = blockData.value.indexOf(data)
    blockData.value.splice(index,1)
  }
}

const handleSubmit=()=> {
  if (!tempData.value.name || !tempData.value.age) {
    alert('姓名和年齡不能為空！')
    return; // 如果有任何一項為空，則不執行後續操作
  }
  if (isEdit.value) {
    
    const index = blockData.value.findIndex(item=>item.id===tempData.value.id) //找到相同id
    if (index !== -1) { 

      blockData.value[index] = tempData.value;
      isEdit.value = false;
    } else {
      console.error("未找到對應的編輯資料");
    }
  } else {
    blockData.value.push({...tempData.value,id:generateUniqueId()})
    tempData.value={
      name: '',
      age: '',
    }
  }
}
</script>

<style lang="scss" scoped>
.q-table th {
  font-size: 20px;
  font-weight: bold;
}

.q-table tbody td {
  font-size: 18px;
}
</style>
