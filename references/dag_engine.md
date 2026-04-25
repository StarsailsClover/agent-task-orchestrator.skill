# DAG Execution Engine

## Overview

DAG（有向无环图）执行引擎负责将 HTN 任务网络转换为可执行的有向无环图，并管理任务的调度与执行。

## Core Components

### 1. DAG Structure

```python
class DAG:
    nodes: Dict[str, Node]      # 任务节点映射
    edges: List[Edge]           # 依赖边列表
    
class Node:
    id: str                     # 节点标识
    task: Task                  # 关联任务
    status: Status              # 执行状态
    inputs: Dict                # 输入数据
    outputs: Dict               # 输出数据
    
class Edge:
    from_node: str              # 源节点
    to_node: str                # 目标节点
    condition: Optional[str]    # 执行条件
```

### 2. Execution States

```
PENDING → READY → RUNNING → COMPLETED
                    ↓
                FAILED → RETRYING
```

状态说明：
- **PENDING**: 等待依赖完成
- **READY**: 依赖已满足，可执行
- **RUNNING**: 正在执行
- **COMPLETED**: 执行成功
- **FAILED**: 执行失败
- **RETRYING**: 重试中

## Execution Algorithm

### Phase 1: DAG Construction

```python
def build_dag(htn_network):
    dag = DAG()
    
    # 1. 创建节点
    for task in htn_network.get_all_tasks():
        if task.type == "primitive":
            dag.add_node(Node(id=task.task_id, task=task))
    
    # 2. 创建边
    for task in htn_network.get_all_tasks():
        for dep_id in task.dependencies:
            dag.add_edge(Edge(from_node=dep_id, to_node=task.task_id))
    
    # 3. 验证无环
    if has_cycle(dag):
        raise CyclicDependencyError("DAG contains cycles")
    
    # 4. 拓扑排序
    dag.execution_order = topological_sort(dag)
    
    return dag
```

### Phase 2: Execution Scheduling

```python
def execute_dag(dag, context):
    completed = set()
    running = set()
    failed = set()
    
    while len(completed) < len(dag.nodes):
        # 1. 找出就绪节点
        ready_nodes = [
            node for node in dag.nodes.values()
            if node.status == Status.PENDING
            and all(dep in completed for dep in get_dependencies(node))
        ]
        
        # 2. 并行执行就绪节点
        for node in ready_nodes:
            if node.task.parallelizable:
                schedule_parallel(node, context)
            else:
                schedule_sequential(node, context)
        
        # 3. 等待状态更新
        wait_for_status_update()
        
        # 4. 处理失败节点
        for node in failed:
            handle_failure(node, dag, context)
    
    return collect_results(dag)
```

### Phase 3: Parallel Execution

```python
def schedule_parallel(nodes, context):
    """并行执行多个节点"""
    with ThreadPoolExecutor() as executor:
        futures = {
            executor.submit(execute_node, node, context): node
            for node in nodes
        }
        
        for future in as_completed(futures):
            node = futures[future]
            try:
                result = future.result()
                node.status = Status.COMPLETED
                node.outputs = result
            except Exception as e:
                node.status = Status.FAILED
                node.error = e
```

## Dependency Management

### Dependency Types

1. **Hard Dependency**: 必须完成后才能执行
   ```yaml
   dependencies:
     - task_id: "t1"
       type: "hard"
   ```

2. **Soft Dependency**: 建议先完成，但非强制
   ```yaml
   dependencies:
     - task_id: "t1"
       type: "soft"
   ```

3. **Conditional Dependency**: 满足条件时才需要
   ```yaml
   dependencies:
     - task_id: "t1"
       type: "conditional"
       condition: "output.format == 'json'"
   ```

### Dependency Resolution

```python
def check_dependencies(node, dag, completed):
    """检查节点依赖是否满足"""
    for dep in node.task.dependencies:
        dep_node = dag.nodes[dep.task_id]
        
        if dep.type == "hard":
            if dep_node.status != Status.COMPLETED:
                return False
        elif dep.type == "conditional":
            if not evaluate_condition(dep.condition, dep_node.outputs):
                return False
        # soft dependency 不影响执行
    
    return True
```

## Error Handling

### Failure Recovery Strategies

| 策略 | 描述 | 适用场景 |
|------|------|---------|
| 立即重试 | 失败后立即重试 | 临时性错误 |
| 延迟重试 | 按退避策略延迟后重试 | 资源暂时不可用 |
| 降级执行 | 使用简化方案执行 | 高级功能不可用 |
| 跳过继续 | 跳过失败任务继续执行 | 非关键任务 |
| 终止流程 | 停止整个 DAG 执行 | 关键任务失败 |

### Error Propagation

```python
def handle_node_failure(node, dag, context):
    """处理节点执行失败"""
    
    # 1. 记录失败信息
    log_failure(node)
    
    # 2. 根据策略处理
    if node.task.retry_policy:
        if should_retry(node):
            schedule_retry(node)
            return
    
    if node.task.fallback:
        execute_fallback(node, dag, context)
        return
    
    # 3. 标记失败并传播
    node.status = Status.FAILED
    propagate_failure(node, dag)
```

## Performance Optimization

### 1. Critical Path Optimization

```python
def calculate_critical_path(dag):
    """计算关键路径"""
    # 使用最长路径算法
    distances = {node: 0 for node in dag.nodes}
    
    for node in topological_order(dag):
        for neighbor in get_neighbors(node):
            distances[neighbor] = max(
                distances[neighbor],
                distances[node] + node.task.estimated_duration
            )
    
    return max(distances, key=distances.get)
```

### 2. Resource-Aware Scheduling

```python
def schedule_with_resources(dag, resources):
    """考虑资源限制的调度"""
    available_resources = resources.copy()
    
    for node in dag.execution_order:
        required = node.task.resource_requirements
        
        # 等待资源可用
        while not can_allocate(available_resources, required):
            wait_for_resource_release()
        
        allocate_resources(available_resources, required)
        execute_node(node)
```

## Monitoring & Observability

### Execution Metrics

```python
@dataclass
class ExecutionMetrics:
    total_nodes: int
    completed_nodes: int
    failed_nodes: int
    retry_count: int
    start_time: datetime
    end_time: Optional[datetime]
    critical_path_duration: timedelta
```

### Progress Tracking

```python
def track_progress(dag):
    """跟踪执行进度"""
    total = len(dag.nodes)
    completed = sum(1 for n in dag.nodes.values() if n.status == Status.COMPLETED)
    
    progress = {
        "percentage": completed / total * 100,
        "completed": completed,
        "total": total,
        "current_stage": get_current_stage(dag)
    }
    
    return progress
```
