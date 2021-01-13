<template>
  <el-tree :data="menus" show-checkbox :props="defaultProps" :expand-on-click-node="false" :default-expanded-keys="expandedKeys" node-key="catId">
    <span class="custom-tree-node" slot-scope="{ node, data }">
        <span>{{ node.label }}</span>
        <span>
          <el-button v-if="node.level <= 2" type="text" size="mini" @click="() => append(data)"> 新增 </el-button>
          <el-button v-if="node.childNodes.length === 0" type="text" size="mini"
                     @click="() => remove(node, data)"> 删除 </el-button>
        </span>
      </span>
  </el-tree>
</template>

<script>
  //这里可以导入其他文件（比如： 组件， 工具 js， 第三方插件 js， json 文件， 图片文件等等）
  //例如： import 《组件名称》 from '《组件路径》 ';

  export default {
    //import 引入的组件需要注入到对象中才能使用
    components: {},
    props: {},
    data() {
      return {
        menus: [],
        expandedKeys:[],
        defaultProps: {
          children: "children",
          label: "name",
        },
      };
    },
    //计算属性 类似于 data 概念
    computed: {},
    //监控 data 中的数据变化
    watch: {},
    //方法集合
    methods: {
      getMenus() {
        this.$http({
          url: this.$http.adornUrl("/product/category/list/tree"),
          method: "get",
        }).then(({data}) => {
          this.menus = data.data
        });
      },
      append(data) {
        console.log("add", data)
      },

      remove(node, data) {
        console.log("remove", node, data)
        var ids = [data.catId]
        this.$confirm(`是否删除【${data.name}】菜单?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/product/category/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  //1、刷新菜单树
                  this.getMenus()
                  //2、设置默认展开菜单
                  this.expandedKeys = [node.parent.data.catId]
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          }).catch(() => {
          })
        })


      },
    },
    //生命周期 - 创建完成（可以访问当前 this 实例）
    created() {
    },
    //生命周期 - 挂载完成（可以访问 DOM 元素）
    mounted() {
    },
    beforeCreate() {
    }, //生命周期 - 创建之前
    beforeMount() {
    }, //生命周期 - 挂载之前
    beforeUpdate() {
    }, //生命周期 - 更新之前
    updated() {
    }, //生命周期 - 更新之后
    beforeDestroy() {
    }, //生命周期 - 销毁之前
    destroyed() {
    }, //生命周期 - 销毁完成
    activated() {
      this.getMenus();
    }, //如果页面有 keep-alive 缓存功能， 这个函数会触发
  };
</script>
<style lang='scss' scoped>
  //@import url(); 引入公共 css 类
</style>
