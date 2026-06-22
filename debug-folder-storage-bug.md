# Debug Session: Folder Storage Bug

## Session ID
`folder-storage-bug`

## Issue Description
用户反馈设置了本地文件夹后，图片仍然无法正确保存和加载。

## Symptoms
1. 点击文件夹按钮选择文件夹后，图片仍不显示
2. 上传图片时出现 "directory could not be found" 错误
3. IndexedDB 存储的目录句柄在使用时失效

## Hypotheses

### Hypothesis 1: IndexedDB 存储的目录句柄在跨会话后失效
- **Description**: 目录句柄存储到 IndexedDB 后，重新加载页面时虽然能读取到，但权限已失效或句柄已过期
- **Expected Log**: initFileSystem 中权限验证返回 'denied' 或 'prompt'
- **Verification**: 检查权限验证结果日志

### Hypothesis 2: 子文件夹创建失败
- **Description**: `tcg-card-images` 子文件夹创建失败或无法访问
- **Expected Log**: selectImageDirectory 中创建子文件夹失败
- **Verification**: 检查子文件夹创建日志

### Hypothesis 3: 异步执行顺序问题
- **Description**: 图片保存时文件系统句柄尚未完全初始化
- **Expected Log**: saveImageToFile 中 useFileSystem 为 false
- **Verification**: 检查图片上传时的文件系统状态

### Hypothesis 4: IndexedDB 升级逻辑问题
- **Description**: 数据库升级时没有正确创建 object store
- **Expected Log**: openDB 升级回调未执行或执行失败
- **Verification**: 检查数据库升级日志

### Hypothesis 5: 文件系统 API 权限问题
- **Description**: 用户选择文件夹后权限未正确授予
- **Expected Log**: queryPermission 返回不是 'granted'
- **Verification**: 检查权限查询日志

## Debug Steps
1. 添加插桩日志到关键函数
2. 复现问题并收集日志
3. 分析日志验证假设
4. 实施修复
5. 验证修复效果

## Status
[OPEN]

---

## Log Analysis (To be filled during debugging)

### Expected Log Flow:

**页面加载时:**
```
[DEBUG] initFileSystem - 从 IndexedDB 获取句柄: 成功/失败
[DEBUG] initFileSystem - 权限验证结果: granted/denied/prompt
[DEBUG] initFileSystem - 成功恢复文件系统访问 / 权限未授予 / IndexedDB 中没有存储的目录句柄
```

**点击文件夹按钮时:**
```
[DEBUG] selectImageDirectory - 开始选择文件夹
[DEBUG] selectImageDirectory - 用户选择了文件夹: xxx
[DEBUG] selectImageDirectory - 创建/获取子文件夹成功: tcg-card-images
[DEBUG] selectImageDirectory - 文件系统状态已更新: { useFileSystem: true, ... }
[DEBUG] selectImageDirectory - 目录句柄已保存到 IndexedDB
```

**上传图片时:**
```
[DEBUG] checkFileSystem - 检查文件系统: { useFileSystem: true, ... }
[DEBUG] validateFileSystem - 开始验证: { useFileSystem: true, ... }
[DEBUG] validateFileSystem - 目录验证成功，包含 X 个项目
[DEBUG] saveImageToFile - 开始保存图片: { useFileSystem: true, ... }
[DEBUG] saveImageToFile - 图片保存成功: xxx.jpg
```

**加载图片时:**
```
[DEBUG] loadImageFromFile - 开始加载图片: { useFileSystem: true, ... }
[DEBUG] loadImageFromFile - 图片加载成功: xxx.jpg
```

### Actual Log Flow (待收集)

---

## Root Cause (待确定)

---

## Fix Applied (待确定)