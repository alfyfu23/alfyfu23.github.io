---
title: Agenta — AI 智能会议助手的开发手记
layout: post
author: alfyfu
date: 2026-06-01 14:00:00 +0800
categories: [项目]
tags: [Vue.js, Python, 语音识别, LLM, Agenta]
---

## 项目背景

开会时经常遇到一个问题：**记笔记和参与讨论无法兼顾**。如果专注听会，笔记就来不及记；如果认真记笔记，就容易跟不上讨论节奏。

于是我想做一个工具，能自动录音 → 转文字 → 生成结构化的会议纪要。

## 技术架构

```
录音文件 → SenseVoice 语音识别 → 文字转写 → LLM 总结 → 结构化纪要
                                    ↑
                              场景模板（问题讨论/项目汇报/学习记录）
```

前端用 **Vue.js + Tailwind CSS**，后端用 **Python**，语音识别用 **SenseVoice**，文本总结用 **LLM**。

### 前端设计

- 上传录音文件
- 选择会议场景模板
- 实时显示转写进度
- 展示生成的会议纪要，支持编辑

### 后端流程

```python
# 1. 语音转写
from SenseVoiceSmall import transcribe
text = transcribe(audio_file)

# 2. 根据场景模板生成纪要
from summary import generate_summary
minutes = generate_summary(text, template="problem")
```

## 场景模板设计

不同类型的会议需要的纪要格式不同，设计了多种模板：

- **问题讨论**：聚焦待解决的问题和决策
- **项目汇报**：进度、风险、下一步
- **学习记录**：知识点、疑问、总结
- **自定义模板**：用户自由配置

## 遇到的挑战

### 挑战 1：长音频转写

会议动辄 1-2 小时，单次转写容易超时或内存不足。

> **方案**：将音频分段处理，最后合并转写结果。
{: .prompt-tip }

### 挑战 2：纪要质量

LLM 生成的纪要有时会遗漏关键信息，或者添加不存在的内容。

> **方案**：设计严格的 prompt，要求 100% 覆盖要点，不额外添加内容。
{: .prompt-tip }

### 挑战 3：多语言支持

会议中可能中英混杂，语音识别需要处理好。

> **方案**：SenseVoice 原生支持多语言识别，直接使用即可。
{: .prompt-tip }

## 总结

这个项目让我深入实践了：
- 语音识别 API 的集成
- LLM prompt 工程
- 前后端分离架构

> 项目源码：[GitHub - Agenta](https://github.com/alfyfu23/Agenta)
{: .prompt-info }
