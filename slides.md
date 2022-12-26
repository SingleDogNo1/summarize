---
theme: seriph
background: https://source.unsplash.com/collection/94734566/1920x1080
class: text-center
highlighter: shiki
lineNumbers: false
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
drawings:
  persist: false
css: unocss
title: 赵晨敏
titleTemplate: '%s年终总结'
favicon: ./favicon.ico
---

# 2022年终报告

软件开发部 -- 赵晨敏

---

# 年度主要工作

<Gantt />

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# 当前弊端

上一页展示了本年主要工作，其中多以类似业务在不同厂的扩展。同样的业务场景在不同的应用场景下逻辑略有不同，因此相同的模块一直存在需要修改少数业务逻辑的地方。此时，应用的主要弊端在：在当我认为只需要修改少部分业务逻辑的情况下，实际上开发量的改动远大于预期。原因主要集中在两点：

- 代码封装不合理
- 业务模块耦合严重

<div class="w-full h-40 mt-4 grid grid-cols-2 gap-4">
  <div class="bg-gray-100 text-sm">
    有的代码过度封装。过渡封装导致修改时逻辑混乱，仿佛所有文件都和逻辑相关联。有的代码又没有封装，我们的系统中使用最多的表格、表单、可视化图表三大功能，都完全没有封装，哪里在用，就写在哪里。导致同样的错误，如果在调度日志发生了，那么在其他地方也会存在同样的错误，而解决的方式需要我们把调度日志的修复方案粘贴到其他模块来解决。如果粘贴过程中遇到了耦合性的问题，还需要处理额外的问题。
  </div>
  <div class="bg-gray-100 text-sm">
    业务的耦合性很高，常发生牵一发动全身的情况。当修复其中一个问题时，会触发其他的问题甚至会干扰到其他的正常逻辑。
  </div>
</div>

<arrow v-click="1" x1="400" y1="420" x2="230" y2="330" color="#564" width="3" arrowSize="1" />

---
layout: image-right
image: https://source.unsplash.com/collection/94734566/1920x1080
---

# 方案

- 封装了<code>Table</code>[^1] 和 <code>Form</code>[^2] 组件，开发中只需要关注需要处理的数据。部分业务无关的逻辑进行了封装（表单重置、点击查询、多行表单展开/折叠等），避免开发过程中冗余的定义以及出现 bug 时需要修改多处的问题。
- 基于之前说到的问题，现在的维护成本是很高的，上述一条也只算是给当前应用打的补丁，不能从根本上改善臃肿的体积和运行时的速度。基于此，我着手搭建了新的<code>ICMES</code>框架[^3]。全新的架构，全新的技术，运行更快，维护成本更低。现已完成基础模块搭建。

[^1]: [table](http://192.168.88.79:8081/ZHAOCHENMIN/element-table-plus)
[^2]: [form](http://192.168.88.79:8081/ZHAOCHENMIN/element-form-plus)
[^3]: [ICMES](http://192.168.88.79:8081/ZHAOCHENMIN/icmes-web-new)

<style>
  ul {
    font-size: 16px;
  }
</style>
---

# 示例

```vue {0|2,7-13|3,15-17|19-23|all}
<template>
  <BasicForm @register="registerForm" @submit="search" />
  <BasicTable @register="registerTable" :loading="loading" />
</template>

<script>
  const [registerForm] = useForm({
    schemas: [
      { field: 'timeRange', label: '起止日期' },
      { field: 'globalName', label: '关键字搜索' },
    ],
    fieldMapToTime: [['timeRange', ['timeStart', 'timeEnd'], 'x']],
  });

  const [registerTable, { setTableData }] = useTable({
    columns: [{ title: '操作用户', dataIndex: 'operator' }]
  });

  function search() {
    const data = searchData()
    loading = false
    setTableData(data)
  }
</script>
```
