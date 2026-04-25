---
name: agent-task-orchestrator
description: This skill is originally designed to fix the native task splitting defects of Step Assistant/Stepclaw, and implement standardized processing throughout the full lifecycle of user requests. Applicable to complex task scenarios requiring multimodal file parsing, dynamic model routing, hierarchical task decomposition and reliable execution, as well as full-cycle hierarchical memory management. This Skill is triggered when user requests involve file/folder processing, tool calls, complex task links, or multi-step coordinated execution. But it's useful, so I think it can be use in other Agents.
---

# Agent Task Orchestrator

**Caution! This skill will take 2-5x tokens waste than you disable it. But it actually work. If you want more features youcan use my OpenOxygen.**

## Overview
This Skill implements a standardized processing workflow for the full lifecycle of user requests and resolves inherent task splitting flaws of the native Assistant. Core capabilities include multimodal file parsing, dynamic model routing, hierarchical task decomposition & reliable execution, and full-cycle hierarchical memory management.

## Execution Flow
All user requests must strictly follow the execution workflow below:

### Phase 1: Request Entry & Preprocessing
```
User Request → Hierarchical Memory Base Loading → Request Standardization → File Inclusion Judgment → Model Scheduling
```

**Step 1: Load System Hierarchical Memory Base**
- Retrieve historical dialogue context
- Query execution records of similar tasks
- Load historical model configuration data
- Read user preference profiles

**Step 2: Request Standardization**
- Unify semantic expression formats
- Remove invalid and redundant information
- Supplement basic semantic context

**Step 3: File Inclusion Judgment** (Branched Logic)

#### Branch 1: Request Contains File Resources
**3.1 Image File Identification**
- If the file is an image:
  1. Execute visual understanding processing
  2. Complete image semantic extraction
  3. Proceed to folder inclusion judgment
- If non-image file: directly proceed to folder inclusion judgment

**3.2 Folder Identification**
- If the resource is a folder:
  1. Conduct file security inspection
  2. Create an exclusive workspace (default if unspecified by the user)
  3. Pull all internal folder resources
  4. Complete file semantic embedding
- If not a folder: proceed to multi-file judgment

**3.3 Multi-file Identification**
- If multiple files are included:
  1. Conduct file security inspection
  2. Create an exclusive workspace
  3. Complete file semantic embedding
- If a single file is included:
  1. Conduct file security inspection
  2. Invoke dedicated tools for direct file parsing
  3. **Important**: Convert floating-point Number values to int32 integers before injection when using reading tools
  4. Complete file semantic embedding

**3.4 Enter the model scheduling workflow**

#### Branch 2: Request Contains No File Resources
1. Perform hierarchical security inspection (content compliance review & risk level assessment)
2. Conduct natural language semantic sorting (complete logical context & clarify core requirement boundaries)
3. Enter the model scheduling workflow

---

### Phase 2: Model Scheduling & Inference Optimization
**Step 1: Verify Tool Availability**
- Detect operational status of all available tools
- Validate tool permission configurations

**Step 2: Confirm LLM Operational Status**
- Evaluate capability boundaries of the current model
- Verify model context window capacity

**Step 3: Execute Task Hierarchy Assessment**

Hierarchy classification is determined by the following dimensions:
- Requirement complexity (Simple / Medium / Complex)
- Requirement for tool/Agent invocation
- Task link length

Classification results map to four execution branches:

| Difficulty | Agent Required | Execution Branch |
|------------|----------------|------------------|
| Simple / Medium | No | Branch 1 |
| Simple / Medium | Yes | Branch 2 |
| Complex | No | Branch 3 |
| Complex | Yes | Branch 4 |

---

### Phase 3: Core Workflow for Hierarchical Task Execution
#### Branch 1: Simple / Medium Difficulty · No Agent Required
```
Light/Medium LLM Invocation → Direct Inference → Response Generation → Short-term Memory Persistence → Result Output
```

**Execution Steps**
1. Invoke matched light/medium-capability LLM for direct reasoning
2. Generate complete responses aligned with user requirements based on inference results
3. Persist short-term memory for the current dialogue (original request, reasoning process, final response)
4. Deliver final output to the user and terminate the workflow

---

#### Branch 2: Simple / Medium Difficulty · Agent Required
```
HTN Planner Initialization → Requirement Clarity Validation → DAG Construction → Environment Perception → Atomic Operation Execution → Real-time Reflection & Verification → Operation Success Validation → Mid-term Memory Persistence / Replanning
```

**Execution Steps**
1. **Initialize HTN Hierarchical Task Network Planner**
   - Decompose user requirements into atomic subtasks
   - Define clear task objectives, execution steps, preconditions and delivery standards

2. **Execute Requirement Clarity Validation**
   - Inspect decomposed tasks for missing information, ambiguous boundaries or vague demands
   - **Unclear Requirements**: Initiate targeted user inquiry → Collect supplementary information → Re-run HTN task decomposition
   - **Clear Requirements**: Proceed to the next step

3. **Construct DAG Directed Acyclic Dependency Graph**
   - Clarify sequential task execution logic
   - Define cross-task dependency relationships
   - Formulate parallel execution rules

4. **Enable Environment Perception Monitoring**
   - Real-time collection: system status, tool availability, permission configurations and resource constraints

5. **Complete Pre-execution Preparation**
   - Adapt execution environment configurations
   - Prepare tool invocation pipelines
   - Verify operational permissions

6. **Determine Optimal Execution Strategy**
   - Leverage DAG dependency graph and real-time environment data
   - Schedule corresponding tools and capabilities to execute atomic operations

7. **Real-time Reflection & Validation** (medium-capability LLM invoked synchronously during execution)
   - Verify consistency between operational outputs and expected objectives
   - Audit operational compliance
   - Identify abnormal risks and hidden hazards

8. **Execute Operation Success Validation**
   - **Execution Succeeded**:
     - Persist mid-term memory (task decomposition schema, execution logs, operational results, reflection records)
     - Deliver final response to the user
     - Terminate the workflow
   - **Execution Failed**:
     - Trigger HTN replanning mechanism
     - Adjust task decomposition schema and execution path based on failure causes and reflection insights
     - Re-execute iteratively until successful completion

---

#### Branch 3: Complex Difficulty · No Agent Required
```
Chain-of-Thought Activation → Asynchronous Collaborative Reasoning → Security Compliance Inspection → Response Generation → Long-term Memory Persistence → Result Output
```

**Execution Steps**
1. **Activate Chain-of-Thought Reasoning**
   - Fully decompose requirement logic
   - Sort out complete inference pathways
   - Build rigorous argumentation frameworks

2. **Enable Asynchronous Collaborative Reasoning Mechanism**
   - Conduct cross-model validation based on CoT inference results
   - Supplement and optimize response content
   - Perform comprehensive risk screening

3. **Conduct Security Compliance Inspection**
   - Prevent prompt injection attacks
   - Enforce content compliance verification

4. **Generate Final Response**
   - Produce logically structured, comprehensive outputs with validated content

5. **Persist Long-term Memory**
   - Record complete data: original requirements, full inference workflow, collaborative reasoning results and finalized responses

6. **Deliver final output to the user** and terminate the workflow

---

#### Branch 4: Complex Difficulty · Agent Required
```
Advanced LLM + HTN Joint Decomposition → Requirement Clarity Validation → Memory Base Retrieval & Optimization → Full-link DAG Construction → Asynchronous Collaborative Validation → Full-cycle Pre-execution Preparation → Phased Execution → End-to-end Real-time Reflection → Operation Success Validation → Long-term Memory Persistence / Full-scale Replanning
```

**Execution Steps**
1. **Enable Advanced LLM + HTN Joint Decomposition Mode**
   - Break down complex requirements into layered, phased atomic subtasks
   - Define overall objectives, phase milestones, atomic tasks, dependency constraints and risk contingency plans

2. **Execute Requirement Clarity Validation**
   - Inspect full-link decomposed tasks for information gaps, ambiguous boundaries or undefined demands
   - **Unclear Requirements**: Initiate targeted user inquiry → Collect supplementary information → Re-run advanced LLM + HTN joint decomposition
   - **Clear Requirements**: Proceed to the next step

3. **Retrieve Hierarchical Memory Base**
   - Recall historical execution cases of analogous complex tasks
   - Reference validated successful solutions
   - Analyze historical failure records and pain points
   - Optimize the current task decomposition schema

4. **Construct Full-link DAG Directed Acyclic Dependency Graph**
   - Standardize end-to-end task execution sequences
   - Define multi-level dependency relationships
   - Configure parallel execution specifications
   - Mark critical milestone nodes

5. **Enable Asynchronous Collaborative Reasoning Mechanism**
   - Conduct cross validation, risk assessment and path optimization for DAG execution schemas
   - Finalize actionable execution plans

6. **Complete Full-cycle Pre-execution Preparation**
   - Adapt multi-scenario execution environments
   - Schedule end-to-end toolchains
   - Conduct full-cycle permission verification
   - Configure abnormal exception monitoring

7. **Phased Task Execution**
   - Execute according to finalized full-link plans
   - Formulate phased delivery strategies
   - Schedule matched tools, capabilities and Agents for atomic task execution

8. **End-to-end Real-time Reflection & Validation** (advanced LLM invoked synchronously)
   - Continuously align operational outputs with long-term objectives
   - Maintain cross-link operational compliance
   - Proactively identify potential systemic risks
   - Dynamically optimize execution details in real time

9. **Execute Operation Success Validation**
   - **Full-link Execution Succeeded**:
     - Persist long-term memory (complete task decomposition schema, end-to-end execution logs, reflection insights, successful case archives and optimization experience)
     - Deliver comprehensive final response to the user
     - Terminate the workflow
   - **Execution Failed**:
     - Trigger full-scale HTN replanning
     - Adjust overall task decomposition and execution pathways based on failure root causes, advanced LLM reflection results and real-time environment changes
     - Re-execute cyclically until full-task completion

---

## Memory Management
### Hierarchical Memory Base Structure
| Tier | Stored Content | Retention Period | Retrieval Strategy |
|------|----------------|------------------|--------------------|
| Short-term Memory | Current dialogue records, basic inference results | Single session | Full content loading |
| Mid-term Memory | HTN task schemas, execution logs, reflection records | Cross-session | Similarity matching retrieval |
| Long-term Memory | Full workflow records of complex tasks, successful cases, optimization experience | Persistent storage | Semantic retrieval + tag filtering |

### Memory Writing Triggers
- **Short-term Memory**: Written immediately after Branch 1 execution completion
- **Mid-term Memory**: Written upon successful operation in Branch 2
- **Long-term Memory**: Written after completion of Branch 3 and Branch 4 workflows

---

## Key Implementation Notes
### File Parsing Specifications
**Number Type Conversion**: When parsing files with reading tools, all floating-point numeric values must be converted to int32 integers prior to injection to eliminate precision loss risks.

### Core HTN Planner Specifications
Each atomic task must contain standardized elements:
- Task Goal
- Execution Steps
- Preconditions
- Deliverables

### DAG Dependency Graph Constraints
- Strictly implemented as a directed acyclic graph with zero cyclic dependencies
- Clear demarcation for parallel execution scope
- Explicit marking of critical path nodes

### Reflection & Validation Checklist
- Consistency between operational results and expected goals
- Operational compliance (permission control & security policies)
- Abnormal risk identification and early warning
- Execution efficiency assessment and optimization

---

## References
Detailed implementation documentation:
- [HTN Planning Guide](references/htn_planning.md) – Full specifications for Hierarchical Task Network Planner
- [DAG Execution Engine](references/dag_engine.md) – Implementation design of directed acyclic graph execution engine
- [Memory Schema](references/memory_schema.md) – Data structure definition for hierarchical memory base
- [Model Routing](references/model_routing.md) – Dynamic model routing strategy specifications
- [Safety Checks](references/safety_checks.md) – Hierarchical risk assessment and compliance checklist
