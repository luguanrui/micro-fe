<template>
    <div id="MICRO-APP"></div>
</template>
<script>
/* eslint-disable */
import util from '@/common/js/util';
import { registerApplication, start, getAppNames, getAppStatus, unloadApplication } from 'single-spa';
// 业务接入
window.BASE_ROUTE = `/${PROJECT_NAME}`; //子模块路由base
export default {
    data() {
        return {};
    },
    methods: {
        registry(key, app) {
            // 去重
            if (getAppNames().includes(key)) {
                return;
            }
            // registerApplication 用于注册我们的子项目，第一个参数为项目名称（唯一即可），第二个参数为项目地址，第三个参数为匹配的路由，第四参数为初始化传值。
            registerApplication(
                key,
                () => {
                    const render = () => {
                        // 渲染
                        return window.System.import(app.path).then(res => {
                            if (res) {
                                console.log(res)
                                return res;
                            } else {
                                return render();
                            }
                        });
                    };
                    return render();
                },
                location => {
                    if (location.pathname.indexOf(app.router) !== -1) {
                        return true;
                    } else {
                        return false;
                    }
                }
            );
        },
        // 注册模块
        async registerApp() {
            const appList = util.getCache('appList');
            for (const key in appList) {
                if (appList.hasOwnProperty(key)) {
                    const app = appList[key];
                    this.registry(key, app);
                }
            }
        }
    },
    async mounted() {
        await start();
        await this.registerApp();
    }
};
</script>
<style lang="less" scoped>
#MICRO-APP {
    position: relative;
    width: 100%;
    height: 100%;
    z-index: 10;
}
</style>
