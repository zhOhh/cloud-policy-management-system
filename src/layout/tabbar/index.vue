<template>
    <div class="tabbar">
        <div class="tabbar_left">
            <!-- 顶部左侧静态 -->
            <el-icon style="margin-right: 10px;" @click="changeIcon">
                <component :is="layoutSettingStore.fold?'Fold':'Expand'"></component>
            </el-icon>
            <!-- 左侧面包屑 -->
            <el-breadcrumb separator-icon="ArrowRight">
                <!-- 动态展示路由名字与标题 -->
                <el-breadcrumb-item v-for="(item, index) in $route.matched" :key="index" v-show="item.meta.title"
                    :to="item.path">
                    <!-- 图标 -->
                    <el-icon>
                        <component :is="item.meta.icon"></component>
                    </el-icon>
                    <!-- 面包屑展示匹配路由的标题 -->
                    <span style="margin: 0px 5px;">{{ item.meta.title }}</span>
                </el-breadcrumb-item>
            </el-breadcrumb>
        </div>
        <div class="tabbar_right">
            <el-button size="small" icon="Refresh" circle @click="updateRefsh"></el-button>
            <el-button size="small" icon="FullScreen" circle @click="fullScreen"></el-button>
            <el-popover placement="bottom" title="主题设置" :width="200" trigger="hover">
                <!-- 表单元素 -->
                <el-form>
                    <el-form-item label="主题颜色">
                        <el-color-picker @change="setColor" v-model="color" size="small" show-alpha
                            :predefine="predefineColors" />
                    </el-form-item>
                    <el-form-item label="暗黑模式">
                        <el-switch @change="changeDark" v-model="dark" class="mt-2" style="margin-left: 24px" inline-prompt
                            active-icon="MoonNight" inactive-icon="Sunny" />
                    </el-form-item>
                </el-form>
                <template #reference>
                    <el-button size="small" icon="Setting" circle></el-button>
                </template>
            </el-popover>

            <img :src="userStore.avatar" style="width:24px;height:24px;margin:0px 10px;border-radius:50%">
            <el-dropdown>
                <span class="el-dropdown-link">
                    {{ userStore.username }}
                    <el-icon class="el-icon--right">
                        <arrow-down />
                    </el-icon>
                </span>
                <template #dropdown>
                    <el-dropdown-menu>
                        <el-dropdown-item @click="logout">退出登录</el-dropdown-item>
                    </el-dropdown-menu>
                </template>
            </el-dropdown>
        </div>
    </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'
// import { Expand } from '@element-plus/icons-vue';
import useLayOutSettingStore from '@/store/modules/setting'
import { useRoute, useRouter } from 'vue-router'
//获取用户相关的小仓库
import useUserStore from '@/store/modules/user'
//获取路由对象
let $route = useRoute()
let $router = useRouter()
//获取layout配置相关仓库
let layoutSettingStore = useLayOutSettingStore()
let userStore = useUserStore()
//收集开关数据
let dark = ref<boolean>(false)
//点击图标的方法
const changeIcon = () => {
    //图标进行切换
    layoutSettingStore.fold = !layoutSettingStore.fold
}
//刷新按钮回调
const updateRefsh = () => {
    layoutSettingStore.refsh = !layoutSettingStore.refsh
}
//全屏按钮点击回调
const fullScreen = () => {
    //DOM对象一个属性：判断当前是否为全屏模式【全屏：true 非全屏：false】
    let full = document.fullscreenElement
    if (!full) {
        //文档根节点的方法requestFullscreen 实现全屏
        document.documentElement.requestFullscreen()
    } else {
        //退出全屏
        document.exitFullscreen()
    }

}
//退出登录点击回调
const logout = async () => {
    await userStore.userLogout()
    $router.push({ path: '/login' })
}
//颜色组件
const color = ref('rgba(255, 69, 0, 0.68)')
const predefineColors = ref([
    '#ff4500',
    '#ff8c00',
    '#ffd700',
    '#90ee90',
    '#00ced1',
    '#1e90ff',
    '#c71585',
    'rgba(255, 69, 0, 0.68)',
    'rgb(255, 120, 0)',
    'hsv(51, 100, 98)',
    'hsva(120, 40, 94, 0.5)',
    'hsl(181, 100%, 37%)',
    'hsla(209, 100%, 56%, 0.73)',
    '#c7158577',
])
// 暗黑模式切换
const changeDark = () => {
    let html = document.documentElement
    dark.value ? html.className = 'dark' : html.className = ''
}
//主题颜色的设置
const setColor = () => {
    //通知js修改根节点的样式对象的属性与属性值
    const html = document.documentElement;
    html.style.setProperty('--el-color-primary', color.value);
}
</script>

<style scoped lang="scss">
.tabbar {
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: space-between;
    background-image: linear-gradient(to right, #ffdf6d, #8e98fe, #ffcaca);

    .tabbar_left {
        display: flex;
        align-items: center;
        margin-left: 20px;
    }

    .tabbar_right {
        display: flex;
        align-items: center;
    }
}
</style>