<template>
  <div class="app-container">

    <data-grid url="/sysmgr/loginlog/list" dataName="listQuery" ref="dataList" @dataRest="onDataRest" >
      <template slot="form">
        <el-form-item label="账号">
          <el-input v-model="listQuery.account" placeholder="账号" class="filter-item" @keyup.enter.native="handleFilter" />
        </el-form-item>
      </template>
      <!--extendOperation-->
      <template slot="extendOperation">
      </template>
      <!--body-->
      <template slot="body">
        <el-table-column type="index" width="50" align="center" :index="indexMethod" fixed="left"></el-table-column>
        <el-table-column align="center" prop="account" label="账号" ></el-table-column>
        <el-table-column label="访问时间">
          <template slot-scope="scope">
            <span>{{ scope.row.loginTime | parseTime('{y}-{m}-{d} {h}:{i}') }}</span>
          </template>
        </el-table-column>
        <el-table-column align="left" prop="content" label="内容" ></el-table-column>
        <el-table-column label="创建时间">
          <template slot-scope="scope">
            <span>{{ scope.row.createdTime | parseTime('{y}-{m}-{d} {h}:{i}') }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="120" class-name="small-padding fixed-width">
          <template slot-scope="scope">
            <el-button type="danger" size="mini" icon="el-icon-delete" @click="dropRow(scope.row)" title="删除" v-show="hasAuthority('sysmgr.loginlog.delete')"></el-button>
          </template>
        </el-table-column>
      </template>
    </data-grid>
  </div>
</template>

<script>
import { getList,drop} from "@/api/sysmgr/loginlog";


import DataGrid from "@/components/DataGrid";
import { parseTime } from '@/utils'
import waves from "@/directive/waves"; // Waves directive

export default {
  name: "sysmgrloginlog",
  components: { DataGrid },
  directives: { waves },
  filters: {
    parseTime
  },
  data() {
    
    return {
      tableKey:0,
      total: 0,
      list: null,
      listLoading: true,
      listQuery: {
        pageNo: 1,
        limit: 10,
        id: null,
        account: null
      }
    };
  },
  methods: {
    indexMethod(index) {
      return index + ((this.listQuery.pageNo-1) * this.listQuery.limit) + 1;
    },
    onDataRest(){
      this.listQuery = {}
    },
    handleFilter() {
      this.$refs.dataList.fetchData();
    },
    dropRow(row){
      this.$confirm('您确定要删除该数据吗?', '提示', {
					confirmButtonText: '确定',
					cancelButtonText: '取消',
					type: 'warning'
				}).then(() => {
          let params={};
          if(row.id){
            params.id= row.id;
            drop(params).then((res) => {
              this.$refs.dataList.fetchData();
            });
          }
				}).catch(() => {
      });
    }
  }
};
</script>
