## 一、引入iconify

1.安装插件

```javascript
yarn add purge-icons-webpack-plugin -D
```

2.添加配置

vue.config.js:

```typescript
const { PurgeIcons } = require("purge-icons-webpack-plugin");

chainWebpack: (config) => {
    config
      .plugin("purgeIcons")
      .use(PurgeIcons)
      .tap((args) => {
        return args;
      });
  },

```

main.ts里引入`import "@purge-icons/generated";`

3.使用

```typescript
import { Icon } from '@iconify/vue';
 
<Icon icon="mdi-light:home" />
```

## 二、使用svg图标

1.安装插件

```javascript
yarn add svg-sprite-loader -D
```

2.添加配置

在vue.config.js里添加：

```javascript
chainWebpack: (config) => {
    config.module
      .rule("icons")
      .test(/\.svg$/)
      .include.add(resolve("src/assets/svg")) // 这里填svg资源的路径，根据实际情况填写
      .end()
      .use("svg-sprite-loader")
      .loader("svg-sprite-loader")
      .options({
        symbolId: "icon-[name]",
      })
      .end();
  },


```

3.在公共组件下创建SvgIcon.vue组件，用来渲染svg图标

```typescript
<template>
  <svg
    :class="[$attrs.class, spin && 'svg-icon-spin']"
    :style="getStyle"
    aria-hidden="true"
  >
    <use :xlink:href="symbolId" />
  </svg>
</template>
<script lang="ts">
import { defineComponent, computed } from "vue";

export default defineComponent({
  name: "SvgIcon",
  props: {
    prefix: {
      type: String,
      default: "icon",
    },
    name: {
      type: String,
      required: true,
    },
    size: {
      type: [Number, String],
      default: 16,
    },
    spin: {
      type: Boolean,
      default: false,
    },
  },
  setup(props) {
    const symbolId = computed(() => {
      return props.prefix ? `#${props.prefix}-${props.name}` : `#${props.name}`;
    });

    const getStyle = computed(() => {
      const { size } = props;
      let s = `${size}`;
      s = `${s.replace("px", "")}px`;
      return {
        width: s,
        height: s,
      };
    });
    return { symbolId, getStyle };
  },
});
</script>
<style lang="less">
@prefix-cls: ~"@{ant-prefix}-comp-svg-icon";

.@{prefix-cls} {
  display: inline-block;
  overflow: hidden;
  vertical-align: -0.15em;
  fill: currentColor;
}

.svg-icon-spin {
  animation: loadingCircle 1s infinite linear;
}
</style>

```

把SvgIcon.vur组件全局注册后即可使用：

```html
<svg-icon name="test" />
```



## 三、封装icon组件可渲染svg、iconify图标

```typescript
<template>
  <SvgIcon
    :size="size"
    :name="getSvgIcon"
    prefix=""
    v-if="isSvgIcon"
    :class="[$attrs.class]"
    :spin="spin"
  />
  <span
    v-else
    ref="elRef"
    :class="[$attrs.class, 'app-iconify', spin && 'app-iconify-spin']"
    :style="getWrapStyle"
  ></span>
</template>
<script lang="ts">
import type { PropType } from "vue";
import {
  defineComponent,
  ref,
  watch,
  onMounted,
  nextTick,
  unref,
  computed,
  CSSProperties,
} from "vue";
import SvgIcon from "./SvgIcon.vue";
import Iconify from "@purge-icons/generated";
import { isString } from "@/utils/is";
import { propTypes } from "@/utils/propTypes";

const SVG_END_WITH_FLAG = "|svg";
export default defineComponent({
  name: "Icon",
  components: { SvgIcon },
  props: {
    // icon name
    name: propTypes.string,
    // icon color
    color: propTypes.string,
    // icon size
    size: {
      type: [String, Number] as PropType<string | number>,
      default: 16,
    },
    spin: propTypes.bool.def(false),
    prefix: propTypes.string.def(""),
  },
  setup(props) {
    const elRef = ref<ElRef>(null);

    const isSvgIcon = computed(() => props.name?.endsWith(SVG_END_WITH_FLAG));
    const getSvgIcon = computed(() =>
      props.name.replace(SVG_END_WITH_FLAG, "")
    );
    const getIconRef = computed(
      () => `${props.prefix ? props.prefix + ":" : ""}${props.name}`
    );

    const update = async () => {
      if (unref(isSvgIcon)) return;

      const el = unref(elRef);
      if (!el) return;

      await nextTick();
      const icon = unref(getIconRef);
      if (!icon) return;

      const svg = Iconify.renderSVG(icon, {});
      if (svg) {
        el.textContent = "";
        el.appendChild(svg);
      } else {
        const span = document.createElement("span");
        span.className = "iconify";
        span.dataset.icon = icon;
        el.textContent = "";
        el.appendChild(span);
      }
    };

    const getWrapStyle = computed((): CSSProperties => {
      const { size, color } = props;
      let fs = size;
      if (isString(size)) {
        fs = parseInt(size, 10);
      }

      return {
        fontSize: `${fs}px`,
        color: color,
        display: "inline-flex",
      };
    });

    watch(() => props.name, update, { flush: "post" });

    onMounted(update);

    return { elRef, getWrapStyle, isSvgIcon, getSvgIcon };
  },
});
</script>
<style lang="less">
@iconify-bg-color: #5551;
.app-iconify {
  display: inline-block;
  // vertical-align: middle;

  &-spin {
    svg {
      animation: loadingCircle 1s infinite linear;
    }
  }
}

span.iconify {
  display: block;
  min-width: 1em;
  min-height: 1em;
  background-color: @iconify-bg-color;
  border-radius: 100%;
}
</style>

```

该全局注册后，即可直接使用。

iconify图标使用方法：

```html
<Icon name="emojione-monotone:artist-palette" />
```

svg图标使用方法：

```html
<Icon name="test" prefix="icon" />
```

## 四、引入字体图标库图标

1.在字体图标库切换到`symbol`模式，生成CDN地址（或者把生成的文件下载下来，放在项目静态文件夹里）

2.在`main.ts`里添加如下代码：

```javascript
import { createFromIconfontCN } from "@ant-design/icons-vue";
createFromIconfontCN({
  // scriptUrl: "//at.alicdn.com/t/c/font_3622931_9w8t4m2bkpl.js", // 在 iconfont.cn 上生成
  scriptUrl: "/font/iconfont.js",
});
```

> 注：这里推荐下载字体图标的js文件，放在项目静态文件里，一定要放在静态文件里，不然的话引入路径会报错，找不到资源。

3.以上完成后，就可以使用字体图标库的图标了，结合上面的svg组件使用方法如下：

```html
<Icon name="incfont" prefix="icon"  />
```



