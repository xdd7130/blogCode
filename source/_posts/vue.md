---
title: VUE
date: 2019-7-13 21:35:13
categories:
- VUE
tags: 
    - VUE
---
# IntellIJ IDEA 配置支持Vue
参考：https://www.jianshu.com/p/01d832e028ea
第一步：先安装vue插件:
file --> settings --> plugins，然后什么都不用输入，直接点击Browse repositories... 紧接着左上方输入“vue”，点击搜索结果里的vue.js，右边会有绿色install按钮，安装成功后重启idea，这样idea就能识别.vue文件了。
第二步：设置vue新建文件模板。
# 安装Element-ui 

# vue2.0 + element UI 中 el-table 数据导出Excel
最近后台项目中有需要将游戏用户提交的返利数据信息导出为excel表的形式，所以对此功能进行了简单的总结：
## 安装相关依赖
```bash
主要是两个依赖:(xlsx 和 file-saver)
npm install --save xlsx file-saver
```
对于这两个插件使用，github上边有更加详细的参考
```bash
https://github.com/SheetJS/js-xlsx](https://github.com/SheetJS/js-xlsx)
https://github.com/eligrey/FileSaver.js](https://github.com/eligrey/FileSaver.js
```
## 在组件头里边引入插件(测试的时候，下边代码放到入口js文件main.js的时候没有效果，遗留问题，有待解决)（放到组件头里边能够实现效果）
```bash
import FileSaver from 'file-saver'
import XLSX from 'xlsx'
```
## 在对应组件里边methods里边写一个方法（到处的时候进行调用）
```bash
exportExcel () {
    /* generate workbook object from table */
    let wb = XLSX.utils.table_to_book(document.querySelector('#rebateSetTable'));
    /* get binary string as output */
    let wbout = XLSX.write(wb, { bookType: 'xlsx', bookSST: true, type: 'array' });
    try {
        FileSaver.saveAs(new Blob([wbout], { type: 'application/octet-stream' }), '用户提交返利表.xlsx');
    } catch (e)
    {
        if (typeof console !== 'undefined')
            console.log(e, wbout)
    }
    return wbout
 },
```
提示：
上边方法中：XLSX.uitls.table_to_book( 放入的是table 的DOM 节点 ) ，sheetjs.xlsx 即为导出表格的名字，可修改！
## 点击导出按钮执行 exportExcel 的方法即可 。
```bash
<div class="export">
     <el-button @click="exportExcel" style="margin-top: 2px;" size="medium" type="success">导出</el-button>
</div>
```