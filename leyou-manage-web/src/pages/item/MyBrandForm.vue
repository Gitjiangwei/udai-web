<template>
  <v-form v-model="valid" ref="MyBrandForm">
    <v-container>
      <v-row>
        <v-col cols="12" sm="6" md="4">
          <v-text-field label="请输入品牌名称" :rules="nameRules" v-model="brand.name" required></v-text-field>
      </v-col>
        <v-col cols="12" sm="6" md="4">
          <v-text-field label="请输入品牌首字母" :rules="letterRules" v-model="brand.letter" required></v-text-field>
        </v-col>
        <v-col cols="12" sm="6" md="4">
          <v-cascader
            url="udai-item/qryList?userToken=1"
            multiple = "true"
            required
            v-model="brand.categories"
            aria-label="选择商品品牌"
          />
        </v-col>
        <v-col cols="12" sm="6" md="4">
            <v-layout row>
                <v-flex xs3>
                      <span style="font-size: 16px;color: #444444">品牌Logo：</span>
                </v-flex>
                <v-flex>
                  <v-upload
                    v-model="brand.image"
                    url="/upload"
                    :multiple="false"
                    :pic-width="250"
                    :pic-height="90"
                  />
                </v-flex>
            </v-layout>
        </v-col>
      </v-row>
    </v-container>
      <v-card-actions>
        <v-layout row>
          <v-spacer></v-spacer>
          <v-btn  @click="clear">重置</v-btn>
          <v-btn color="primary"  @click="submit">提交</v-btn>
        </v-layout>
      </v-card-actions>
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
        },
        nameRules: [
          v => !!v || '品牌名称不能为空',
          v => (v && v.length>1)|| '品牌名称至少两位',
          v => /^[^<>%;./\\+#$!=\s*￥)(&*"':@_]*$/.test(v) || '存在特殊字符',
        ],
        letterRules:[
          v => !!v || '品牌首字母不能为空',
          v => (v && v.length<2)|| '首字母只能是一位',
          v => /^[A-Z]{1}$/.test(v) || "品牌字母只能是A~Z的大写字母",
          v => /^[^<>%;./\\+#$!=\s*￥)(&*"':@_]*$/.test(v) || '存在特殊字符',
        ]
      }

    },
    methods: {
      submit(){
          //1、表单校验
        if(this.$refs.MyBrandForm.validate()){
            //2、定义一个请求参数，通过结构表达式来获取Brand中的属性
            const {categories,letter,...params} = this.brand;
            //3、数据库中只要保存分类的id即可，因此我们对categories的值进行处理，只保留id，并转为字符串
            params.cids = categories.map(c => c.id).join(",");
            //4、将字符都处理为大写
            params.letter = letter.toUpperCase();
            //5、将数据太提交到后台
            //由于后台需要的请求体是一个对象，那么直接传params会自动编译成json形式，所以使用第三方插件QS（Query String）来转换成对象进行发送
          /*axios 处理请求体的原则会根据请求数据的格式来定：
               如果请求体是对象：会转为 json 发送
               如果请求体是 String：会作为普通表单请求发送，但需要我们自己保证 String 的格式是键值对。
               如：name=jack&age=12*/
            this.$http.post("udai-item/saveBrand?userToken=1",this.$qs.stringify(params))
              .then(() => {
                //6、回调成功提示
                this.$message.success("保存成功！");
              }).catch(() =>{
                //6、失败提示
                this.$message.success("保存失败！");
            })
        }
      },
      created(){
          console.log(this.$qs);
      },
      clear(){

          this.$refs.MyBrandForm.reset();

          this.categories=[];
      },
    }
  }

</script>
