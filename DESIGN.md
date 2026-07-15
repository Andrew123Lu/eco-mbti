# 环保MBTI改造设计文档

## 现有架构分析

### 可复用组件
1. **页面切换框架**：`showScreen()` / `navigateTo()` / `screens` 对象
2. **题目渲染逻辑**：`renderQuestions()` / `updateProgress()` 
3. **选项点击事件处理**：radio button change listener
4. **单页应用结构**：`intro` → `test` → `result` 流程
5. **进度条实现**：CSS + JS 宽度控制
6. **结果页渲染框架**：`renderResultFromData()`

### 必须重写
1. **题目数据**：30题 → 12题（完全不同的内容和场景）
2. **计分逻辑**：15维度pattern匹配 → 3维度简单求和+阈值切割
3. **结果类型**：27种 → 8种（完全不同的描述和画像）
4. **视觉风格**：绿色系 → 淡蓝+绿色系
5. **新增功能**：序章、章节过渡、雷达图、分享卡片、数据埋点

## 新数据层设计

### 12题数据结构
```javascript
const questions = [
  {
    id: 'q1',
    chapter: 1, // 第一章
    chapterTitle: '日常惯性',
    location: '深圳大学城',
    scenario: '周五凌晨1点...',
    options: [
      { label: '...', ac: 1, ar: 1, pn: 1 },
      { label: '...', ac: 2, ar: 1, pn: 3 },
      { label: '...', ac: 4, ar: 1, pn: 2 },
      { label: '...', ac: 2, ar: 4, pn: 2 }
    ]
  }
];
```

### 8型人格结果
```javascript
const RESULT_TYPES = {
  '000': { name: '碳局外人', slogan: '...', desc: '...', advice: '...' },
  // ... 其他7种
};
```

### 三维度元数据
```javascript
const dimensionMeta = {
  AC: { name: '结果意识', fullName: 'Awareness of Consequences', desc: '...' },
  AR: { name: '责任归属', fullName: 'Attribution of Responsibility', desc: '...' },
  PN: { name: '个体规范', fullName: 'Personal Norm', desc: '...' }
};
```

## 新UI层设计

### 视觉风格
- 主背景：#e8f4f8（淡蓝）→ #f0f7f4（淡蓝绿）渐变
- 卡片背景：#ffffff
- 主色调：#2e8b57（海绿）, #3cb371（中海绿）
- 强调色：#87ceeb（天蓝）, #20b2aa（浅海绿）
- 文字色：#1a3a3a（深青）
- 辅助色：#6a9a8f（灰绿）
- 进度条：从 #d0d0d0 到 #2e8b57
- 边框：#b8d8d0
- 阴影：0 8px 32px rgba(46, 139, 87, 0.08)
- 圆角：20px

### 小碳碳IP
- 头像：CSS绘制的淡蓝色碳原子精灵（简化版，用emoji🌱+🧚代替或CSS绘制）
- 对话框：带小尖角的聊天气泡
- 动画：hover弹跳、打字机动画
- 文案风格：毒舌但心软

### 序章页面
- 全屏淡蓝渐变背景
- 小碳碳头像居中（带脉冲动画）
- 邀请函文案（打字机效果）
- "开始测试"按钮（绿色渐变）

### 章节过渡
- 每3题后显示（第3、6、9题后）
- 小碳碳弹出提示
- 淡入动画
- 点击"继续"进入下一章

## 新结果页设计

### 三维度雷达图
- 使用Canvas绘制
- 三个顶点：AC、AR、PN
- 用户得分区域填充（半透明绿色）
- 满分线（虚线）
- 维度标签

### 最短板分析
- 自动识别最低维度
- 显示对应干预策略
- 个性化建议

### 分享卡片
- Canvas生成PNG
- 尺寸：1:1（1080×1080）
- 包含：人格名称、标语、雷达图、最短板自嘲文案
- 下载按钮

### 地球球长跳转
- "进入地球球长模拟器"按钮
- 占位链接（先显示"Coming Soon"）

## 数据埋点
- localStorage存储：
  - `eco_mbti_answers`: 选项选择
  - `eco_mbti_result`: 最终人格类型
  - `eco_mbti_share_count`: 分享次数
  - `eco_mbti_complete_time`: 完成时间
