 先说一下需求，需要换肤的包括顶部导航、侧导航、内容区域的主题色。导航的配色是比较私有化的，用ant现有的颜色变量不能满足UI的需求，基于这个换肤需求我采用了顶部导航和测导航用css变量（var()变量） + 主内容区域ant-design-vue的动态换肤实现。

## 一、 css变量

先新建一个 `theme.less`的文件，定义导航主题色变量，在写导航的css样式时颜色用这里定义的变量。例如：`background-color: @top-menu-active-bg-color;`

```less
@menu-text-color: var(--menuTextColor, #fff);
@top-menu-bg: var(--topMenuBg, #005cb8);
@top-menu-active-bg-color: var(--topMenuActiveBgColor, #004390);
@aside-menu-text-color: var(--asideMenuTextColor, #fff);
@aside-menu-border-color: var(--asideMenuBorderColor, transparent);
@aside-menu-bg: var(--asideMenuBg, #273754);
@aside-menu-active-bg: var(--asideMenuActiveBg, #1D4E8C);
@aside-menu-active-border-color: var(--asideMenuActiveBorderColor, #0874F0);
@aside-submenu-border-color: var(--asideSubmenuBorderColor, #0B1D3E);
@aside-menu-hover-bg: var(--asideMenuHoverBg, #4b5d7e);
@aside-menu-expand-bg: var(--asideMenuExpandBg, #0B1D3E);
```

再创建一个多主题色的配置文件`model.ts`：

```typescript
export const themeConfig = {
  dark: {
    // menu
    menuTextColor: "#fff",
    topMenuBg: "#005cb8",
    topMenuActiveBgColor: "#004390",
    asideMenuTextColor: "#fff",
    asideMenuBorderColor: "transparent",
    asideMenuBg: "#273754",
    asideMenuActiveBg: "#1D4E8C",
    asideMenuActiveBorderColor: "#0874F0",
    asideSubmenuBorderColor: "#0B1D3E",
    asideMenuHoverBg: "#4b5d7e",
    asideMenuExpandBg: "#0B1D3E",
  },
  light: {
    // menu
    menuTextColor: "#fff",
    topMenuBg: "#1CA0FF",
    topMenuActiveBgColor: "#1C8DFD",
    asideMenuTextColor: "#596376",
    asideMenuBorderColor: "#D0D5D8",
    asideMenuBg: "#fff",
    asideMenuActiveBg: "#E6F8FF",
    asideMenuActiveBorderColor: "#1C8DFD",
    asideSubmenuBorderColor: "transparent",
    asideMenuHoverBg: "#E6F8FF",
    asideMenuExpandBg: "#F6F7FA",
  },
};
```

配置好之后，重点的切换来了，新建一个`useTheme.ts`文件

```typescript
import { themeConfig } from "./model";
import { useAppStore } from "@/store/modules/app";
// import { ConfigProvider } from "ant-design-vue";

// 修改样式变量值
const changeStyle = (obj: Object) => {
  for (const key in obj) {
    document
      .getElementsByTagName("body")[0]
      .style.setProperty(`--${key}`, obj[key]);
  }
};
// 改变主题的方法
export const useTheme = (themeName: string) => {
  const appStore = useAppStore();
  appStore.setThemeName(themeName);
  const theme = themeConfig[themeName];
  changeStyle(theme);
  return theme;
};

```

切换主题的时候调用useTheme方法即可，导航就换肤就实现了

```typescript
const handleSwitchTheme = (theme: string) => {
      useTheme(theme);
    };
```

## 二、 antdv动态换肤

antdv的版本是3.2.12的，可以用antdv自带的[ConfigProvider](https://www.antdv.com/components/config-provider/#components-config-provider-demo-theme) 组件来实现动态换肤，官方的步骤是

1. 替换当前项目引入样式文件为 CSS Variable 版本：

   ```typescript
   -- import 'ant-design-vue/dist/antd.min.css';
   ++ import 'ant-design-vue/dist/antd.variable.min.css';
   ```

   > 注：如果你使用了 `babel-plugin-import`，需要将其去除。

   2.调用 ConfigProvider 配置方法设置主题色：

   ```typescript
   import { ConfigProvider } from 'ant-design-vue';
   
   ConfigProvider.config({
     theme: {
       primaryColor: '#25b864',
     },
   });
   ```

   以上是官方给出动态换肤方案，结合当前项目，我在`model.ts`里加上了主题的配置：

   ```typescript
   export const themeConfig = {
     dark: {
       // menu
       ...,
       primaryColor: "#005CB8",
       errorColor: "#ff4d4f",
       warningColor: "#faad14",
       successColor: "#52c41a",
       infoColor: "#1890ff",
     },
     light: {
       // menu
       ...,
       primaryColor: "#1C8DFD",
       errorColor: "#f5dbdb",
       warningColor: "#f1c773",
       successColor: "#b5f396",
       infoColor: "#98c5ef",
     },
   };
   
   ```

   通过调用`useTheme`方法得到主题配色：

   ```typescript
   import { ConfigProvider } from 'ant-design-vue';
   
   const handleSwitchTheme = (theme: string) => {
         const {
           primaryColor,
           errorColor,
           warningColor,
           successColor,
           infoColor,
         } = useTheme(theme);
       
         ConfigProvider.config({
           theme: {
             primaryColor,
             errorColor,
             warningColor,
             successColor,
             infoColor,
           },
         });
       };
   ```

   

   

