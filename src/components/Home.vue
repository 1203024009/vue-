<template>
<el-container class="home-container">
  <!-- 头部区域 -->
  <el-header>
      <div>
          <img src="../assets/heima.png" alt="">
          <span>高校项目评审系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
  </el-header>
  <!-- 页面主体区域 -->
  <el-container>
    <!-- 侧边栏 -->
    <el-aside :width="isCollapse ? '64px' : '200px'">
        <div class="toggle-button" @click="toggleCollapase">|||</div>
        <!--侧边栏菜单区域-->
        <el-menu
      background-color="#333744"
      text-color="#fff"
      active-text-color="#409eff"
      unique-opened
      :collapse = "isCollapse"
      :collapse-transition = "false"
      router 
      :default-active='activePath'
      > <!--router启用该模式会在激活导航时以 index 作为 path 进行路由跳转-->
      <!-- 一级菜单 -->
      <el-submenu :index="item.id + ''" v-for="item in menulist" :key="item.id">
          <!-- 一级菜单的模板区域 -->
        <template slot="title">
          <!-- 图标 -->
          <i :class="iconsObj[item.id]"></i>
          <!-- 文本 -->
          <span>{{item.authName}}</span>
        </template>
        <!-- 二级菜单 -->
        <el-menu-item :index="'/' + subItem.path" v-for="subItem in item.children"
        :key="subItem.id" @click="saveNavState('/' + subItem.path)">
            <template slot="title">
          <!-- 图标 -->
          <i class="el-icon-menu"></i>
          <!-- 文本 -->
          <span>{{subItem.authName}}</span>
        </template>
        </el-menu-item>
        </el-submenu>
    </el-menu>
    </el-aside>
    <!-- 右侧内容主体 -->
    <el-main>
        <!-- 路由占位符 -->
        <router-view></router-view>
    </el-main>
  </el-container>
</el-container>


</template>

<script> 
export default{
    data(){
        return{
            //左侧菜单数据
            menulist:[],
            iconsObj:{
                '125':'el-icon-user',
                '103':'el-icon-s-check',
                '101':'el-icon-s-platform',
                '102':'el-icon-s-order',
                '145':'el-icon-s-data'
            },
            //默认不折叠
            isCollapse: false,
            //被激活的链接地址
            activePath: ''
        }
    },
    created(){
        this.getMenulist()
        this.activePath = window.sessionStorage.getItem('activePath')
    },
    methods:{
    logout(){
       window.sessionStorage.removeItem('token');//删除一个token
       this.$router.push('/login'); 
    },
    //获取所有的菜单
    async getMenulist(){
        const { data: res} = await this.$http.get('menus') //获取data的值
        if(res.meta.status !== 200) return this.$message.error(res.meta.msg)
        this.menulist = res.data
        console.log(res)
    },
    //点击按钮切换菜单的折叠与展开
    toggleCollapase(){
        this.isCollapse = !this.isCollapse
    },
    //保存链接的激活状态
    saveNavState(activePath){
        window.sessionStorage.setItem('activePath',activePath)
        this.activePath = activePath
    }
}
};

</script>

<style lang = "less" scoped>
.home-container {
    height: 100vh;
}
.el-header {
    background-color: #373d41;
    display: flex;
    justify-content: space-between;//两端对齐
    padding-left: 0%;//图标左侧对齐
    align-items: center;//退出按键居中
    color: #fff;
    font-size: 20px;//字体大小
    > div {
        display: flex;
        align-items: center;//字体居中
        span{
            margin-left: 15px;//距离左侧图像十五像素
        }
    }
}
.el-aside {
    background-color: #333744;
    .el-menu{
        border-right: none;
    }
    transition-property: width;
    transition-duration: 0.8s;
}

.el-main {
    background-color: #eaedf1;
}

.iconfont {
    margin-right: 0px;
}

.toggle-button{
    background-color: #4a5064;
    font-size: 10px;
    line-height: 24px;//行高
    color: #fff;
    text-align: center;
    letter-spacing: 0.2em;//三条线的间距
    cursor: pointer;
}
</style>