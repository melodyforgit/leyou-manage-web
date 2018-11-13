<template>
  <v-card>
    <template>
      <div>
        <v-card-title>
          <v-btn color="primary" @click="show = true">新增品牌</v-btn>
          <v-spacer/>
          <v-text-field
            label="请输入搜索条件"
            append-icon="search"
            single-line
            v-model="search"
          ></v-text-field>
        </v-card-title>
        <v-divider/>
        <v-data-table
          :headers="headers"
          :items="brands"
          :pagination.sync="pagination"
          :total-items="totalBrands"
          :loading="loading"
          class="elevation-1"
        >
          <template slot="items" slot-scope="props">
            <td class="text-xs-center">{{ props.item.id }}</td>
            <td class="text-xs-center">{{ props.item.name }}</td>
            <td class="text-xs-center"><img :src=" props.item.image "/></td>
            <td class="text-xs-center">{{ props.item.letter }}</td>
            <td class="justify-center layout px-0">
              <v-btn color="primary">编辑</v-btn>
              <v-btn color="warning">删除</v-btn>
            </td>

          </template>
        </v-data-table>
        <v-dialog scrollable v-model="show" max-width="500" persistent>
          <v-card>
            <v-toolbar dense color="primary" dark class="title px-2"><span>新增品牌</span>
              <v-spacer/>
              <v-btn icon @click="show=false">
                <v-icon>close</v-icon>
              </v-btn>
            </v-toolbar>
            <v-card-text style="height: 500px;" class="px-5">
              <my-brand-form/>
            </v-card-text>
          </v-card>
        </v-dialog>
      </div>
    </template>
  </v-card>
</template>
<script>
  import MyBrandForm from "./MyBrandForm";

  export default {
    name: "my-brand",
    components: {MyBrandForm},
    data() {
      return {
        headers: [
          {text: "id", value: "id", align: 'center', sortable: true,},
          {text: "名称", value: "name", align: 'center', sortable: false,},
          {text: "LOGO", value: "image", align: 'center', sortable: false,},
          {text: "首字母", value: "letter", align: 'center', sortable: false,},
          {text: "操作", align: 'center', sortable: false,}
        ],
        brands: [],
        pagination: {},
        totalBrands: 0,
        loading: false,
        search: '',
        show: false,//窗口显示控制
      }
    },
    watch: {
      pagination: {
        deep: true,
        handler() {
          this.getDataFromServer();
        }
      },
      search() {
        this.getDataFromServer();
      }
    },
    created() {
      this.getDataFromServer();
    },
    methods: {
      getDataFromServer() {
        //开启进度条
        this.loading = true;
        //发起ajax请求 page，rows，search查询条件key，descending是否降序desc，sortby排序字段

        this.$http.get("/item/brand/page", {
          params: {
            key: this.search, // 搜索条件
            page: this.pagination.page,// 当前页
            rows: this.pagination.rowsPerPage,// 每页大小
            sortBy: this.pagination.sortBy,// 排序字段
            desc: this.pagination.descending// 是否降序
          }
        }).then(resp => {
          this.brands = resp.data.items;
          this.totalBrands = resp.data.total;
          this.loading = false;
        })
      }
    }

  }
</script>

<style scoped>

</style>
