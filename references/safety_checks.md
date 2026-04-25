# Safety Checks

## Overview

安全分级与合规检查清单，确保所有任务执行符合安全策略和内容规范。

## Safety Levels

### Level 1: Basic Safety (基础安全)

**检查项：**
- 输入长度限制
- 特殊字符过滤
- 基础格式校验

**处理策略：**
- 自动清理
- 格式标准化

### Level 2: Content Safety (内容安全)

**检查项：**
- 敏感词检测
- 恶意代码识别
- 隐私信息保护
- 版权内容检查

**处理策略：**
- 内容脱敏
- 请求拦截
- 人工审核标记

### Level 3: Operational Safety (操作安全)

**检查项：**
- 工具调用权限
- 文件系统访问
- 网络请求安全
- 资源使用限制

**处理策略：**
- 权限校验
- 沙箱执行
- 资源配额控制

## Check Categories

### 1. Input Validation

```yaml
input_checks:
  length_check:
    max_input_length: 100000
    max_attachment_size: 50MB
    
  format_check:
    allowed_encodings: ["UTF-8", "ASCII"]
    blocked_mime_types: ["executable", "script"]
    
  structure_check:
    validate_json: true
    validate_xml: true
    max_nesting_depth: 10
```

### 2. Content Compliance

```yaml
content_checks:
  sensitive_data:
    patterns:
      - regex: "\\b\\d{4}[ -]?\\d{4}[ -]?\\d{4}[ -]?\\d{4}\\b"  # 信用卡
        type: "credit_card"
        action: "mask"
      - regex: "\\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\\.[A-Z|a-z]{2,}\\b"  # 邮箱
        type: "email"
        action: "mask"
      - regex: "\\b\\d{3}-\\d{2}-\\d{4}\\b"  # SSN
        type: "ssn"
        action: "block"
        
  prohibited_content:
    categories:
      - "illegal_activities"
      - "harmful_instructions"
      - "personal_attacks"
      - "discriminatory_content"
    action: "block_and_log"
    
  copyright_check:
    check_against_known_works: true
    similarity_threshold: 0.85
    action: "flag_for_review"
```

### 3. Prompt Injection Detection

```yaml
prompt_injection_checks:
  attack_patterns:
    - pattern: "ignore previous instructions"
      severity: "high"
    - pattern: "system prompt"
      severity: "high"
    - pattern: "DAN|jailbreak"
      severity: "critical"
    - pattern: "\\[\\s*INST\\s*\\]|<<SYS>>"
      severity: "high"
      
  delimiters_check:
    look_for: ["```", '"""', "<|", "|>", "[/", "/]"]
    context_window: 200
    
  semantic_check:
    enabled: true
    model: "safety-classifier"
    threshold: 0.8
```

### 4. Tool Call Safety

```yaml
tool_safety:
  permission_matrix:
    file_read:
      allowed_paths: ["/workspace/*", "/tmp/*"]
      blocked_patterns: ["*.key", "*.pem", ".env"]
      
    file_write:
      allowed_paths: ["/workspace/output/*"]
      max_file_size: 100MB
      require_confirmation: true
      
    network_request:
      allowed_domains: ["api.trusted.com", "cdn.example.com"]
      blocked_domains: ["localhost", "127.0.0.1", "*.internal"]
      max_request_size: 10MB
      timeout: 30s
      
    code_execution:
      allowed_languages: ["python", "javascript"]
      sandboxed: true
      resource_limits:
        max_memory: "512MB"
        max_cpu_time: "30s"
        max_processes: 5
```

## Risk Assessment Matrix

| 风险类型 | 等级 | 检测方式 | 响应动作 |
|---------|------|---------|---------|
| 输入超长 | 低 | 长度检查 | 截断+警告 |
| 敏感信息 | 中 | 模式匹配 | 脱敏处理 |
| 注入攻击 | 高 | 语义+模式 | 拦截+记录 |
| 越权访问 | 高 | 权限校验 | 拒绝+告警 |
| 恶意代码 | 严重 | 多维度检测 | 隔离+上报 |

## Safety Check Pipeline

```python
class SafetyPipeline:
    def __init__(self):
        self.checks = [
            InputValidationCheck(),
            ContentComplianceCheck(),
            PromptInjectionCheck(),
            ToolSafetyCheck()
        ]
    
    def execute(self, context):
        results = []
        
        for check in self.checks:
            result = check.run(context)
            results.append(result)
            
            # 严重问题立即拦截
            if result.severity == "critical":
                return SafetyResult(
                    passed=False,
                    blocked=True,
                    reason=result.message,
                    action="block_and_alert"
                )
            
            # 高风险需要确认
            if result.severity == "high":
                return SafetyResult(
                    passed=False,
                    blocked=False,
                    reason=result.message,
                    action="require_confirmation"
                )
        
        return SafetyResult(passed=True)
```

## Incident Response

### Response Levels

```yaml
response_levels:
  level_1_info:
    triggers: ["minor_format_issue"]
    action: "log_only"
    
  level_2_warning:
    triggers: ["sensitive_data_detected"]
    action: "sanitize_and_log"
    
  level_3_alert:
    triggers: ["suspicious_pattern"]
    action: "flag_and_notify"
    
  level_4_block:
    triggers: ["confirmed_injection", "malicious_code"]
    action: "block_and_escalate"
```

### Escalation Flow

```
检测异常 → 分级评估 → 执行响应 → 记录日志 → 通知相关方 → 定期复盘
```

## Audit & Compliance

### Logging Requirements

```yaml
audit_logging:
  retention_period: "90d"
  log_fields:
    - timestamp
    - request_id
    - user_id
    - check_type
    - result
    - severity
    - action_taken
    - metadata
```

### Compliance Standards

- GDPR: 个人数据处理合规
- SOC 2: 安全控制审计
- ISO 27001: 信息安全管理
