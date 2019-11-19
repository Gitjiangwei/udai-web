<template xmlns:v-slot="http://www.w3.org/1999/XSL/Transform">
  <div>

    <!--卡片的头部-->
    <v-card-title>
      <v-btn depressed  color="primary">新增品牌</v-btn>
      <v-spacer />

      <v-text-field label="输入关键字搜索"   hide-details="false"  append-icon="search" v-model.lazy="search"  ></v-text-field>


    </v-card-title>
    <!--分割线-->
    <v-divider/>
    <!--卡片中部-->
    <v-data-table
      :headers="headers"
      :items="desserts"
      :pagination.sync="pagination"
      :total-items="totalDesserts"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img src="props.item.image"/></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-btn flat icon color="info">
            <v-icon>edit</v-icon>
          </v-btn>
          <v-btn flat icon color="error">
            <v-icon>delete</v-icon>
          </v-btn>
        </td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
    import VSpec from "./specification/Specification";
    export default {
        name: "MyBrand",
      components: {VSpec},
      data(){
          return{
            headers:[
              { text:"品牌Id", value:"id", align:"center", sortable:"true"},
              { text:"品牌名称", value:"name", align:"center", sortable:"false"},
              { text:"品牌Logo", value:"image", align:"center", sortable:"false"},
              { text:"品牌首字母", value:"letter", align:"center", sortable:"true"},
              { text:"操作", align:"center", sortable:"false"},
            ],
            desserts:[ ],//当前数据
            pagination:{}, //分页项目信息
            totalDesserts:0,
            loading: true, //是否在加载中
            search: '', //搜索过滤字段
          }
      },

      created(){ //渲染后执行
/*        this.desserts = [
          {id: 2032, name: "OPPO", image: "1.jpg", letter: "0"},
          {id: 2033, name: "飞利浦", image: "2.jpg", letter: "F"},
          {id: 2034, name: "华为", image: "3.jpg", letter: "H"},
          {id: 2035, name: "酷派", image: "4.jpg", letter: "K"},
          {id: 2036, name: "魅族", image: "5.jpg", letter: "M"},
        ];
        this.totalDesserts = 15;*/
        //查询数据
        this.loadBrands();



      },
      watch:{
          pagination:{//监视pagination属性的变化
            deep:true, //当deep为true时，会监视pagination的属性及属性中的对象属性变化
            handler(){
              //变化后的回调函数，这里我们再次调用getDataFromServer即可
              this.loadBrands();
            },
            key(){
              this.loadBrands();
            }
          },
          search:{
            //监视搜索字段
            handler(){

              this.loadBrands();
            }
          }


      },
      methods:{
        loadBrands: function () {
          this.$http.get("udai-item/qryBrand?userToken=1", {
            params: {
              name: this.search,
              pages: this.pagination.page, //当前页
              rows: this.pagination.rowsPerPage, //每页长度
              sortBy: this.pagination.sortBy, //是否排序
              desc: this.pagination.descending, // 是否降序

            }
          }).then( resp =>{ //这里使用箭头函数
                this.desserts = resp.data;
                this.totalDesserts = resp.data.total;
                //完成赋值后，把加载状态赋值为false
                this.loading = false;
          }).catch( function (error) {
          })
        }
      },
    }
</script>

<style scoped>

</style>
