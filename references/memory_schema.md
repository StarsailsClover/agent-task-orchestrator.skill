# Memory Schema

## Overview

分层记忆库的数据结构定义，支持短期、中期、长期三级记忆管理。

## Memory Hierarchy

```
┌─────────────────────────────────────────────────────────────┐
│                     Memory System                           │
├──────────────┬──────────────┬───────────────────────────────┤
│ Short-term   │ Medium-term  │ Long-term                     │
│ (Session)    │ (Cross-      │ (Persistent)                  │
│              │  Session)    │                               │
├──────────────┼──────────────┼───────────────────────────────┤
│ Current      │ Task plans   │ Complex task                  │
│ conversation │ Execution    │ execution                     │
│ Simple       │ history      │ histories                     │
│ reasoning    │ Reflections  │ Success                       │
│ results      │              │ patterns                      │
│              │              │ Optimization                  │
│              │              │ insights                      │
└──────────────┴──────────────┴───────────────────────────────┘
```

## Schema Definitions

### 1. Short-term Memory

存储当前会话的上下文信息。

```yaml
short_term_memory:
  session_id: "uuid"
  created_at: "timestamp"
  updated_at: "timestamp"
  
  # 对话历史
  conversation_history:
    - turn_id: 1
      role: "user"
      content: "用户输入"
      timestamp: "2024-01-01T10:00:00Z"
      attachments: []  # 附件信息
    - turn_id: 2
      role: "assistant"
      content: "助手回复"
      timestamp: "2024-01-01T10:00:05Z"
  
  # 简单推理记录
  reasoning_logs:
    - log_id: "uuid"
      request: "用户请求"
      reasoning_process: "推理过程"
      result: "推理结果"
      branch_type: "branch_1"  # 执行分支类型
      timestamp: "timestamp"
  
  # 当前上下文
  current_context:
    user_intent: "用户意图"
    extracted_entities: []
    pending_tasks: []
```

### 2. Medium-term Memory

存储跨会话的任务执行信息。

```yaml
medium_term_memory:
  entry_id: "uuid"
  created_at: "timestamp"
  last_accessed: "timestamp"
  access_count: 0
  
  # 任务元信息
  task_metadata:
    task_type: "file_processing|code_generation|data_analysis"
    complexity: "simple|medium"
    requires_agent: true|false
    
  # HTN 任务方案
  htn_plan:
    mission: "任务总目标"
    decomposition_tree: {}  # 完整的任务分解树
    dag_structure: {}       # DAG 结构
    
  # 执行过程
  execution_trace:
    - step_id: 1
      task_id: "t1"
      action: "执行的操作"
      tool_used: "使用的工具"
      input: {}
      output: {}
      duration_ms: 1000
      status: "success|failure"
      timestamp: "timestamp"
  
  # 反思记录
  reflection_logs:
    - reflection_id: "uuid"
      checkpoint: "检查点"
      expected_outcome: "预期结果"
      actual_outcome: "实际结果"
      consistency_check: true|false
      compliance_check: true|false
      risk_identified: []
      optimization_suggestions: []
      timestamp: "timestamp"
  
  # 执行结果
  final_result:
    success: true|false
    output: "最终输出"
    metrics:
      total_steps: 10
      success_steps: 9
      retry_count: 2
      total_duration_ms: 50000
```

### 3. Long-term Memory

存储持久化的复杂任务知识和经验。

```yaml
long_term_memory:
  entry_id: "uuid"
  created_at: "timestamp"
  updated_at: "timestamp"
  
  # 任务分类标签
  tags:
    - "complex_task"
    - "multi_agent"
    - "file_processing"
    - "code_generation"
  
  # 任务摘要
  task_summary:
    title: "任务标题"
    description: "任务描述"
    complexity: "complex"
    domain: "领域"
    
  # 完整执行流程
  full_execution_flow:
    # 需求分析
    requirement_analysis:
      original_request: "原始请求"
      clarified_requirements: "澄清后的需求"
      ambiguity_resolved: []
      
    # HTN 拆解方案
    htn_decomposition:
      phases: []
      critical_path: []
      risk_points: []
      
    # 执行全流程
    execution_timeline:
      - phase: "phase_1"
        tasks: []
        milestones: []
        
    # 反思结果
    reflection_summary:
      key_insights: []
      lessons_learned: []
      
    # 最终输出
    final_output:
      content: "最终内容"
      format: "输出格式"
      quality_score: 0.95
      
  # 成功案例
  success_patterns:
    - pattern_id: "uuid"
      pattern_name: "成功模式名称"
      applicable_conditions: []
      execution_strategy: {}
      success_rate: 0.95
      
  # 优化经验
  optimization_insights:
    - insight_id: "uuid"
      category: "performance|accuracy|robustness"
      description: "优化经验描述"
      before_metrics: {}
      after_metrics: {}
      improvement_percentage: 20
      
  # 踩坑记录
  failure_lessons:
    - lesson_id: "uuid"
      failure_type: "类型"
      root_cause: "根本原因"
      impact: "影响"
      recovery_strategy: "恢复策略"
      prevention_measures: []
```

## Retrieval Strategies

### 1. Short-term Memory Retrieval

```python
def retrieve_short_term(session_id):
    """全量加载当前会话记忆"""
    return load_by_session_id(session_id)
```

### 2. Medium-term Memory Retrieval

```python
def retrieve_medium_term(query, top_k=5):
    """基于相似度检索"""
    # 1. 计算查询向量
    query_embedding = embed(query)
    
    # 2. 相似度匹配
    candidates = search_by_similarity(query_embedding, top_k * 2)
    
    # 3. 过滤与排序
    results = []
    for candidate in candidates:
        score = calculate_relevance_score(query, candidate)
        if score > threshold:
            results.append((candidate, score))
    
    # 4. 返回 Top-K
    return sorted(results, key=lambda x: x[1], reverse=True)[:top_k]
```

### 3. Long-term Memory Retrieval

```python
def retrieve_long_term(query, filters=None):
    """语义检索 + 标签过滤"""
    # 1. 标签预过滤
    if filters and filters.tags:
        candidates = filter_by_tags(filters.tags)
    else:
        candidates = get_all_entries()
    
    # 2. 语义检索
    query_embedding = embed(query)
    semantic_matches = semantic_search(query_embedding, candidates)
    
    # 3. 多维度排序
    scored_results = []
    for match in semantic_matches:
        score = (
            0.4 * match.semantic_score +
            0.3 * match.success_rate +
            0.2 * match.recency_score +
            0.1 * match.access_frequency
        )
        scored_results.append((match, score))
    
    return sorted(scored_results, key=lambda x: x[1], reverse=True)
```

## Memory Lifecycle

### Writing Triggers

| Memory Type | Trigger Condition | Retention Policy |
|-------------|------------------|------------------|
| Short-term | 每次对话后 | 会话结束自动清理 |
| Medium-term | Branch 2 成功后 | 30天无访问归档 |
| Long-term | Branch 3/4 完成后 | 永久保留 |

### Consolidation Flow

```
Short-term → [Pattern Detection] → Medium-term
Medium-term → [Success Analysis] → Long-term
```

### Memory Pruning

```python
def prune_memory():
    """定期清理过期记忆"""
    # 1. 清理过期短期记忆
    expired_short = find_expired_short_term(ttl=SESSION_TTL)
    delete_batch(expired_short)
    
    # 2. 归档冷中期记忆
    cold_medium = find_cold_medium_term(threshold_days=30)
    archive_batch(cold_medium)
    
    # 3. 压缩长期记忆
    compress_long_term(older_than_days=365)
```
