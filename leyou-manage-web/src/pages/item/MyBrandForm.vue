<template>
  <v-form v-model="valid">
    <v-container>
      <v-row>
        <v-col cols="12" sm="6" md="4">
          <v-text-field label="请输入品牌名称" v-model="brand.name" required></v-text-field>
      </v-col>
        <v-col cols="12" sm="6" md="4">
          <v-text-field label="请输入品牌首字母" v-model="brand.letter" required></v-text-field>
        </v-col>
        <v-col cols="12" sm="6" md="4">
          <v-cascader
            url="udai-item/qryList?userToken=1"
            muliple
            required
            v-model="brand.categories"
            aria-label="选择商品品牌"
          />
        </v-col>
      </v-row>
    </v-container>
  </v-form>
</template>
<script>
  export default{
    name: "My-Brand-Form",
    data(){
      return{
        valid:false, //表单标记
        brand:{
          name:'',  //名称
          letter:'', //首字母
          image:'', //logo
          categories:[], //品牌所属的商品分类数组
        }
      }

    }
  }

</script>
