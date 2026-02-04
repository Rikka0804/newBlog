---
title: 通过Map处理数据更新
date: 2025-09-18
image: cover.png
categories:
  - 工作
tags:
  - Vue2

---


在处理列表新增时遇到一个这个问题，通过组件选中的数据来新增，已经勾选过的组件还是勾上的，带出的数据是选中的数据，这时就有问题你不能简单的通过将选中的数据push到一个数组里面就可以，当用户选了一个数据又再次勾选时，你会有重复数据，当用户取消勾选时你获取到的是空数组，也不能使用直接将选择出来的数组直接替代，如果用户有编辑数据会导致数据丢失影响体验，这个时候就需要使用Map数据结构来进行处理了

```javascript
checkInspection(checkNames, checkData) {

      // 首先 创建一个Map
      const existingMap = new Map();
      this.checkInspectionList.forEach(item => {
        if (item.keypointItemId) {
          // 将列表里面的id为键 将数组进行保存
          existingMap.set(item.keypointItemId, item);
        }
      });

      // 处理逻辑：
      // 1. 仅保留checkData中存在的项（自动删除缺失项）
      // 2. 对存在的项进行属性更新
      this.checkInspectionList = checkData.map(item => {
        const { keypointItemId } = item;

        
				// 这块判断勾选的数据之前已经存在就将数据return出去
        // 复用并更新已有项（保持引用一致性）
        if (existingMap.has(keypointItemId)) {
          const existingItem = existingMap.get(keypointItemId);
          return { ...existingItem, ...item };
        }

        // 新增项初始化 
        return { ...item, isProblem: 0, isDelete: 1, delFlag: 0, problemType: 1, };
      });
    },
```

这块是直接对组件的数据进行map遍历，这样就做到了删除，更新的操作，``existingMap.has``存在则复用数据


