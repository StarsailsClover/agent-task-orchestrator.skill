# HTN (Hierarchical Task Network) Planning Guide

## Overview

HTN 规划器用于将复杂用户需求分解为可执行的原子任务网络。本指南定义标准化的任务拆解规范。

## Core Concepts

### 任务层级结构

```
Mission (任务总目标)
  └── Phase (阶段)
        └── Task (任务)
              └── Subtask (子任务)
                    └── Action (原子操作)
```

### 任务节点属性

每个任务节点必须包含以下属性：

```yaml
task_id: "唯一标识符"
name: "任务名称"
description: "任务描述"
type: "composite|primitive"  # 复合任务或原子任务
goal: "任务目标"
preconditions: # 前置条件列表
  - "条件1"
  - "条件2"
steps: # 执行步骤
  - "步骤1"
  - "步骤2"
deliverables: # 交付标准
  - "交付物1"
  - "交付物2"
dependencies: # 依赖的任务ID列表
  - "task_id_1"
  - "task_id_2"
parallelizable: true|false  # 是否可并行执行
estimated_duration: "预估耗时"
retry_policy: # 重试策略
  max_retries: 3
  backoff_strategy: "exponential"
risk_factors: # 风险因素
  - "风险1"
  - "风险2"
```

## Task Decomposition Patterns

### Pattern 1: Sequential Decomposition

适用于有明确先后顺序的任务链：

```yaml
mission: "创建项目文档"
phases:
  - phase_id: "p1"
    name: "需求分析"
    tasks:
      - task_id: "t1_1"
        name: "收集需求"
        type: primitive
        dependencies: []
      - task_id: "t1_2"
        name: "整理需求"
        type: primitive
        dependencies: ["t1_1"]
  - phase_id: "p2"
    name: "文档编写"
    tasks:
      - task_id: "t2_1"
        name: "编写大纲"
        type: primitive
        dependencies: ["t1_2"]
      - task_id: "t2_2"
        name: "编写正文"
        type: primitive
        dependencies: ["t2_1"]
```

### Pattern 2: Parallel Decomposition

适用于可并行执行的独立任务：

```yaml
mission: "多文件处理"
phases:
  - phase_id: "p1"
    name: "文件解析"
    tasks:
      - task_id: "t1_1"
        name: "解析文件A"
        type: primitive
        dependencies: []
        parallelizable: true
      - task_id: "t1_2"
        name: "解析文件B"
        type: primitive
        dependencies: []
        parallelizable: true
      - task_id: "t1_3"
        name: "合并结果"
        type: primitive
        dependencies: ["t1_1", "t1_2"]
        parallelizable: false
```

### Pattern 3: Conditional Decomposition

适用于需要根据条件选择执行路径的任务：

```yaml
mission: "智能数据处理"
phases:
  - phase_id: "p1"
    name: "数据预处理"
    tasks:
      - task_id: "t1_1"
        name: "检测数据类型"
        type: primitive
        branches:
          - condition: "type == 'structured'"
            next_task: "t1_2a"
          - condition: "type == 'unstructured'"
            next_task: "t1_2b"
      - task_id: "t1_2a"
        name: "结构化数据处理"
        type: primitive
        dependencies: ["t1_1"]
      - task_id: "t1_2b"
        name: "非结构化数据处理"
        type: primitive
        dependencies: ["t1_1"]
```

## Replanning Strategy

### 触发条件

1. 任务执行失败
2. 前置条件不满足
3. 依赖任务超时
4. 环境变化导致原方案不可行
5. 用户修改需求

### 重规划流程

```
检测失败 → 分析原因 → 选择策略 → 生成新方案 → 验证可行性 → 执行新方案
```

### 重规划策略

| 策略 | 适用场景 | 操作 |
|------|---------|------|
| 局部修复 | 单任务失败，影响范围小 | 仅重试/替换失败任务 |
| 子树重规划 | 某分支失败 | 重新规划该分支下所有任务 |
| 全局重规划 | 根任务失败或需求变更 | 重新生成完整任务网络 |
| 降级执行 | 高级功能不可用 | 使用备选方案简化执行 |

### 重规划模板

```yaml
replanning_event:
  trigger: "failure|timeout|condition_change"
  failed_task_id: "t1"
  failure_reason: "具体失败原因"
  impact_analysis:
    affected_tasks: ["t2", "t3"]
    critical_path_impact: true|false
  strategy: "local_fix|subtree_replan|global_replan|degraded"
  new_plan:
    # 新生成的任务网络
  lessons_learned:
    - "经验1"
    - "经验2"
```

## Integration with DAG

HTN 任务网络转换为 DAG 的规则：

1. **节点映射**：每个原子任务映射为 DAG 节点
2. **边映射**：任务依赖关系映射为 DAG 边
3. **并行识别**：标记 parallelizable=true 的任务可并行执行
4. **关键路径**：计算最长依赖链作为关键路径

## Best Practices

1. **任务粒度**：原子任务应控制在 5-15 分钟内完成
2. **依赖最小化**：减少不必要的依赖关系
3. **前置条件明确**：每个任务的前置条件必须可验证
4. **交付标准量化**：交付标准应包含可验证的指标
5. **风险预判**：提前识别可能失败的任务并准备备选方案
