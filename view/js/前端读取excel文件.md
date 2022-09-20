先下载插件

API地址：https://docs.sheetjs.com/

```javascript
yarn add xlsx
```

下载上传组件

```javascript
<!-- 导入导出组件（纯前端） -->
<template>
  <span>
    <input
      type="file"
      @change="importFile(this)"
      id="imFile"
      style="display: none"
      accept="application/vnd.openxmlformats-officedocument.spreadsheetml.sheet, application/vnd.ms-excel"
    />
    <a id="downlink"></a>
    <a-space>
      <span @click="downloadFile">
        <slot name="export">
          <a-button class="button">导出</a-button>
        </slot>
      </span>
      <span @click="uploadFile">
        <slot name="import">
          <a-button class="button">导入</a-button>
        </slot>
      </span>
    </a-space>

    <!-- 错误信息提示 -->
    <a-modal title="提示" v-model:visible="errorDialog" size="tiny">
      <span>{{ errorMsg }}</span>
      <template #footer>
        <a-button type="primary" @click="errorDialog = false">确认</a-button>
      </template>
    </a-modal>
  </span>
</template>

<script>
var XLSX = require("xlsx");
export default {
  name: "ProgExportImport",
  data() {
    return {
      fullscreenLoading: false, // 加载中
      imFile: "", // 导入文件el对象
      outFile: "", // 导出文件el对象
      errorDialog: false, // 错误信息弹窗
      errorMsg: "", // 错误信息内容
      excelTitle: {}, // excel标题
      excelData: [], // excel处理数据
    };
  },
  props: {
    setExportData: {
      type: Function,
      default: function () {
        console.warn("未传递获取excel导出数据方法");
      },
    },
    getImportData: {
      type: Function,
      default: function () {
        console.warn("未传递设置excel导入数据方法");
      },
    },
  },
  mounted() {
    this.imFile = document.getElementById("imFile");
    this.outFile = document.getElementById("downlink");
  },
  methods: {
    uploadFile: function () {
      // 导入文件点击事件
      this.imFile.click();
    },
    downloadFile: function () {
      // 导出文件点击事件
      let exportData = this.setExportData();
      this.excelTitle = exportData.excelTitle;
      this.excelData = exportData.excelData;
      let data = [{}, {}];
      for (let k in this.excelData[0]) {
        // 设置第1行为数据库字段行
        // eslint-disable-next-line no-prototype-builtins
        if (this.excelData[0].hasOwnProperty(k)) {
          data[0][k] = k;
          data[1][k] = this.excelTitle[k]; // 中文标题
        }
      }
      data = data.concat(this.excelData);
      this.downloadExl(data, exportData.excelName || "导出文件");
    },
    importFile: function () {
      // 导入excel
      this.fullscreenLoading = true;
      let obj = this.imFile;
      if (!obj.files) {
        this.fullscreenLoading = false;
        return;
      }
      var f = obj.files[0];
      var reader = new FileReader();
      let $t = this;
      reader.onload = function (e) {
        var data = e.target.result;
        if ($t.rABS) {
          $t.wb = XLSX.read(btoa(this.fixdata(data)), {
            // 手动转化
            type: "base64",
          });
        } else {
          $t.wb = XLSX.read(data, {
            type: "binary",
          });
        }
        let json = XLSX.utils.sheet_to_json($t.wb.Sheets[$t.wb.SheetNames[0]]);
        console.log("json:", json);
        const rows = XLSX.utils.sheet_to_json(
          $t.wb.Sheets[$t.wb.SheetNames[0]],
          { header: 1 }
        );
        console.log("rows:", rows);
        /* column objects are generated based on the worksheet range */
        const range = XLSX.utils.decode_range(
          $t.wb.Sheets[$t.wb.SheetNames[0]]["!ref"] || "A1"
        );
        const columns = Array.from({ length: range.e.c + 1 }, (_, i) => ({
          /* for an array of arrays, the keys are "0", "1", "2", ... */
          field: String(i),
          /* column labels: encode_col translates 0 -> "A", 1 -> "B", 2 -> "C", ... */
          label: XLSX.utils.encode_col(i),
        }));
        console.log("columns:", columns);
        $t.dealFile(json);
      };
      if (this.rABS) {
        reader.readAsArrayBuffer(f);
      } else {
        reader.readAsBinaryString(f);
      }
    },
    downloadExl: function (json, downName, type) {
      // 导出到excel
      let keyMap = []; // 获取键
      for (let k in json[0]) {
        // eslint-disable-next-line no-prototype-builtins
        if (json[0].hasOwnProperty(k)) {
          keyMap.push(k);
        }
      }
      let tmpdata = []; // 用来保存转换好的json
      json
        .map((v, i) =>
          keyMap.map((k, j) =>
            Object.assign(
              {},
              {
                v: v[k] || "",
                position:
                  (j > 25 ? this.getCharCol(j) : String.fromCharCode(65 + j)) +
                  (i + 1),
              }
            )
          )
        )
        .reduce((prev, next) => prev.concat(next))
        .forEach(function (v) {
          tmpdata[v.position] = {
            v: v.v,
          };
        });
      let outputPos = Object.keys(tmpdata); // 设置区域,比如表格从A1到D10
      let tmpWB = {
        SheetNames: ["sheet"], // 保存的表标题
        Sheets: {
          sheet: Object.assign(
            {},
            tmpdata, // 内容
            {
              "!ref": outputPos[0] + ":" + outputPos[outputPos.length - 1], // 设置填充区域
            }
          ),
        },
      };
      let tmpDown = new Blob(
        [
          this.s2ab(
            XLSX.write(
              tmpWB,
              { bookType: type || "xlsx", bookSST: false, type: "binary" } // 这里的数据是用来定义导出的格式类型
            )
          ),
        ],
        {
          type: "",
        }
      ); // 创建二进制对象写入转换好的字节流
      var href = URL.createObjectURL(tmpDown); // 创建对象超链接
      this.outFile.download = downName + ".xlsx"; // 下载名称
      this.outFile.href = href; // 绑定a标签
      this.outFile.click(); // 模拟点击实现下载
      setTimeout(function () {
        // 延时释放
        URL.revokeObjectURL(tmpDown); // 用URL.revokeObjectURL()来释放这个object URL
      }, 100);
    },
    dealFile: function (data) {
      // 处理导入的数据
      this.imFile.value = "";
      this.fullscreenLoading = false;
      if (data.length <= 0) {
        this.errorDialog = true;
        this.errorMsg = "请导入正确信息";
      } else {
        this.excelData = data;
        this.getImportData(data);
      }
    },
    s2ab: function (s) {
      // 字符串转字符流
      var buf = new ArrayBuffer(s.length);
      var view = new Uint8Array(buf);
      for (var i = 0; i !== s.length; ++i) {
        view[i] = s.charCodeAt(i) & 0xff;
      }
      return buf;
    },
    getCharCol: function (n) {
      // 将指定的自然数转换为26进制表示。映射关系：[0-25] -> [A-Z]。
      let s = "";
      let m = 0;
      while (n > 0) {
        m = (n % 26) + 1;
        s = String.fromCharCode(m + 64) + s;
        n = (n - m) / 26;
      }
      return s;
    },
    fixdata: function (data) {
      // 文件流转BinaryString
      var o = "";
      var l = 0;
      var w = 10240;
      for (; l < data.byteLength / w; ++l) {
        o += String.fromCharCode.apply(
          null,
          new Uint8Array(data.slice(l * w, l * w + w))
        );
      }
      o += String.fromCharCode.apply(null, new Uint8Array(data.slice(l * w)));
      return o;
    },
  },
};
</script>
```

组件引用

```javascript
<template>
  <excel-import
    :set-export-data="setExportData"
    :get-import-data="getImportData"
  >
    <template v-slot:export>
      <a-button type="default" plain size="mini">导出</a-button>
    </template>
    <template v-slot:import>
      <a-button type="primary" plain size="mini">导入</a-button>
    </template>
  </excel-import>
</template>
<script>
import ExcelImport from "@/components/ExcelImport.vue";
export default {
  components: { ExcelImport },
  methods: {
    setExportData() {
      return {
        excelTitle: {
          code: "编号",
          name: "名称",
        },
        excelData: [],
        excelName: "导出",
      };
    },
    getImportData(data) {
      console.log(data);
    },
  },
};
</script>

```

我的demo数据，导入如下的excel表格：

| 姓名 | 性别 | 电话 | 邮箱 | 地址 |
| ---- | ---- | ---- | ---- | ---- |
| 1    |      | 2    | 3    | xxxx |
| 4    |      | 5    | 6    |      |

`importFile`函数里打印的数据分别如下：

**json**:

```javascript
[
    {
        "姓名": 1,
        "电话": 2,
        "邮箱": 3,
        "地址": "xxxx"
    },
    {
        "姓名": 4,
        "电话": 5,
        "邮箱": 6
    }
]
```

**rows**:

```javascript
[
    [
        "姓名",
        "性别",
        "电话",
        "邮箱",
        "地址"
    ],
    [
        1,
        null,
        2,
        3,
        "xxxx"
    ],
    [
        4,
        null,
        5,
        6
    ]
]
```

**columns**:

```javascript
[
    {
        "field": "0",
        "label": "A"
    },
    {
        "field": "1",
        "label": "B"
    },
    {
        "field": "2",
        "label": "C"
    },
    {
        "field": "3",
        "label": "D"
    },
    {
        "field": "4",
        "label": "E"
    }
]
```

