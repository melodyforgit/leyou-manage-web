<template>
  <v-form v-model="valid" ref="brandForm">
    <v-text-field v-model="brand.name" label="品牌名称" required
                  :rules="[ v => !!v || '品牌名称不能为空']"
    ></v-text-field>
    <v-text-field v-model="brand.letter" label="品牌首字母" required
                  mask="A" :rules="[ v => !!v || '品牌字母不能为空']"></v-text-field>
    <v-cascader
      url="/item/category/list"
      multiple
      required
      v-model="brand.categories"
      label="请选择商品分类"/>
    <v-layout row>
      <v-flex xs4 class="subheader">品牌LOGO:</v-flex>
      <v-flex>
        <v-upload
          v-model="brand.image"
          url="/upload/image"
          :multiple="false"
          :pic-width="250"
          :pic-height="90"
        />
      </v-flex>
    </v-layout>
    <v-layout row class="pt-5">
      <v-btn color="primary" @click="submit">提交</v-btn>
      <v-btn color="warning">重置</v-btn>
    </v-layout>
  </v-form>
</template>
<script>
  export default {
    name: "my-brand-form",
    data() {
      return {
        valid: false,
        brand: {
          name: "",
          letter: "",
          image: "",
          category: []
        }
      }
    },
    methods: {
      submit() {
        if (this.$refs.brandForm.validate()) {
          //结构表达式获取品牌中的数据，除了categories以外的都储存在rest中
          const {categories, ...rest} = this.brand;
          //对categories进行处理，得到里面的id数组，赋值给rest中的categories,通过join方法将其拼接为字符串
          rest.categories=categories.map(c=>c.id).join(",");
          //this.$qs.stringify(rest)将对象转为字符串拼接的格式，表单传输过程中无法传json格式数据
          this.$http.post("/item/brand", this.$qs.stringify(rest)).then(resp => {
            console.log("成功")
          }).catch(resp => {
            console.log("失败")
          })
        }
      }
    }
  }
</script>
<style scoped>

</style>
