---
title: JSON 和 excel之间的转换
date: 2025-02-08 14:34:15
categories: 
- 笔记
tags: [node]
---
## 依赖包安装

```bash
yarn add fs xlsx
```

## json转excel
```bash
const fs = require('fs')
const XLSX = require('xlsx')
// 读取JSON文件
const jsonData = JSON.parse(fs.readFileSync('public/static/i18n/zh_CN/index.json', 'utf-8'))
const handleArr = Object.entries(jsonData).map(item => {
  return {
    key: item[0],
    value: item[1]
  }
})
// 转换为工作表
const worksheet = XLSX.utils.json_to_sheet(handleArr)
// 创建工作簿并添加工作表
const workbook = XLSX.utils.book_new()
XLSX.utils.book_append_sheet(workbook, worksheet, 'Sheet1')
// 写入文件
XLSX.writeFile(workbook, 'hbmLang.xlsx')

```

## excel转json
```bash
const XLSX = require('xlsx')
const fs = require('fs')

// 读取 Excel 文件
const workbook = XLSX.readFile('hbmLang.xlsx')
// 选择要转换的工作表
const sheetName = workbook.SheetNames[0] // 假设我们只转换第一个工作表
const worksheet = workbook.Sheets[sheetName]
// 将工作表转换为 JSON
const jsonData = XLSX.utils.sheet_to_json(worksheet)
let objectData = jsonData.reduce((pre, cur) => {
  return {
    ...pre,
    [cur.key]: cur.value
  }
}, {})
// 将 JSON 数据写入文件或控制台输出
fs.writeFileSync('hbmLang.json', JSON.stringify(objectData, null, 2))

```


