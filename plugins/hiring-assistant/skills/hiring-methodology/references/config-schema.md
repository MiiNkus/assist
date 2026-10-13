# Configuration Schema

This document describes all configuration options for the hiring framework.

## Setup

Organization-specific configuration lives in `.org/[org-name]/`:

1. Create your org folder: `.org/my-company/`
2. Copy structure from `.org/default/` or use `config.yaml.example` as starting point
3. The `.org/` folder is gitignored - all org data stays private

### Folder Structure
```
.org/[org-name]/
├── config.yaml                  # Main configuration (incl. role_definitions)
├── matrices/
│   ├── ic_matrix.md             # IC competency expectations
│   └── manager_matrix.md
├── rubrics/
│   ├── ic_rubric.md             # Core 5 IC categories
│   ├── ic_fullstack_addon.md    # Full stack add-on (categories 6 & 7)
│   ├── ic_data_engineer_addon.md # Data engineer add-on
│   ├── ic_backend_addon.md      # Backend add-on
│   ├── ic_devops_addon.md       # DevOps add-on
│   └── manager_rubric.md
└── regional/
    └── [region]_context.md
```

Agents automatically discover the first org folder in `.org/` and load all files within it.

---

## Organization

```yaml
organization:
  name: "Your Company Name"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Your organization name, used in reports and summaries |

---

## Internal Levels

Define your organization's internal grade/leveling structure. These are the actual internal grades that hiring roles map to.

### IC Internal Levels

```yaml
internal_levels:
  ic:
    - grade: "G5"
      title: "Software Engineer"
      experience: "0-4 years"
      hiring_status: "not_hiring"
      description: "Entry level, learning fundamentals"
    - grade: "G6"
      title: "Senior Software Engineer"
      experience: "2-6 years"
      hiring_status: "hiring"
      description: "Independent contributor, owns features"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `grade` | string | Yes | Internal grade code (e.g., "G5", "G6", "G7") |
| `title` | string | Yes | Internal title at this grade |
| `experience` | string | No | Typical experience range |
| `hiring_status` | string | No | One of: `not_hiring`, `hiring`, `priority` |
| `description` | string | No | Brief description of the level |

### Manager Internal Levels

Same structure as IC. Managers may share grade codes with IC (e.g., G7 can be Staff Engineer or Technology Manager).

---

## Competency Matrices

Define the competency dimensions and level expectations. These drive evaluation scoring.

### Structure

```yaml
competency_matrices:
  ic:
    dimensions:
      - name: "Technical Scope"
        question: "Am I building knowledge and recognized as an expert?"
        weight: 1.0
    levels:
      G6:
        technical_scope:
          - "Debugs moderately complex bugs"
          - "Implements medium/large features"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `dimensions` | array | Yes | List of competency dimensions |
| `dimensions[].name` | string | Yes | Display name for the dimension |
| `dimensions[].question` | string | No | Guiding question for evaluation |
| `dimensions[].weight` | float | No | Relative importance (1.0 = standard) |
| `levels` | object | Yes | Expectations keyed by internal grade |
| `levels.[grade].[dimension]` | array | Yes | List of expected competencies |

**Note:** Dimension keys in `levels` should be snake_case versions of dimension names.

### Matrix Files

For detailed competency descriptions, use markdown files in `.org/[name]/matrices/`:
- `ic_matrix.md` - Full IC competency expectations with examples
- `manager_matrix.md` - Full manager competency expectations

These files provide richer context than YAML allows and are referenced by agents during evaluation.

---

## Role Levels

Define your organization's leveling system. Each role level maps to a subfolder in the resumes directory.

### IC (Individual Contributor) Levels

```yaml
role_levels:
  ic:
    - code: "L1"
      name: "Software Engineer"
      subfolder: "Software Engineer (L1)"
      internal_grade: "G5"
      years_experience: "0-2"
      expectations: "Learning, needs guidance"
      role_type: "fullstack"
    - code: "L2-DE"
      name: "Senior Data Engineer"
      subfolder: "Senior Data Engineer (L2)"
      internal_grade: "G6"
      years_experience: "2-5"
      expectations: "Data pipeline ownership"
      role_type: "data-engineer"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `code` | string | Yes | Short code for reports (e.g., "L1", "L2") |
| `name` | string | Yes | Display name for the hiring role |
| `subfolder` | string | Yes | Must match subfolder name in `resumes/developers/new/` |
| `internal_grade` | string | Yes | Maps to an `internal_levels` grade (e.g., "G6") |
| `years_experience` | string | No | Expected years of experience range |
| `expectations` | string | No | Brief description of level expectations |
| `role_type` | string | No | Links to a `role_definitions` entry (default: "fullstack") |

### Manager Levels

```yaml
role_levels:
  manager:
    - code: "M1"
      name: "Engineering Manager"
      subfolder: "Engineering Manager"
      internal_grade: "G7"
      team_size: "5-8"
      expectations: "Single team management"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `code` | string | Yes | Short code (e.g., "M1", "M2") |
| `name` | string | Yes | Display name |
| `subfolder` | string | Yes | Must match subfolder in `resumes/managers/new/` |
| `internal_grade` | string | Yes | Maps to an `internal_levels` grade |
| `team_size` | string | No | Expected team size at this level |
| `expectations` | string | No | Brief description |

---

## Role Definitions

Define specialized IC roles with role-specific tech tags and add-on rubric categories. Each `role_type` in `role_levels` should reference a key here.

```yaml
role_definitions:
  fullstack:
    display_name: "Full Stack Engineer"
    short_name: "fullstack"
    description: "End-to-end web application development"
    tech_tags: ["frontend", "backend", "api", "database"]
    addon_categories: ["frontend_ui", "api_integration"]
    rubric_file: "ic_fullstack_addon.md"

  data-engineer:
    display_name: "Data Engineer"
    short_name: "data-engineer"
    description: "Data pipelines, ETL, and data infrastructure"
    tech_tags: ["data-pipelines", "etl", "data-warehouse", "sql", "python"]
    addon_categories: ["data_modeling", "pipeline_engineering"]
    rubric_file: "ic_data_engineer_addon.md"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `display_name` | string | Yes | Human-readable role name |
| `short_name` | string | Yes | Used in evaluation folder names (kebab-case) |
| `description` | string | No | Brief description of the specialization |
| `tech_tags` | array | Yes | Tags used to filter tech_stack to relevant technologies |
| `addon_categories` | array | No | Internal names for role-specific rubric categories |
| `rubric_file` | string | Yes | Filename of the add-on rubric in `.org/[org]/rubrics/` |

### How Role Definitions Work

1. Resume subfolder name maps to `role_levels.ic[].subfolder`
2. That entry's `role_type` maps to a key in `role_definitions`
3. Evaluator loads core rubric + the add-on rubric from `rubric_file`
4. Tech stack is filtered to only items with matching `tech_tags`
5. Evaluation folder includes `short_name` as suffix

---

## Thresholds

Define scoring thresholds for resume evaluations and interviews.

### IC Thresholds

```yaml
thresholds:
  ic:
    max_score: 35
    categories: 7
    exceptional: 33    # 94%+ ⭐
    strong: 30         # 86%+ 💪
    acceptable: 26     # 74%+ ✅
    borderline: 22     # 63%+ ⚠️
```

| Field | Type | Required | Description | Emoji |
|-------|------|----------|-------------|-------|
| `max_score` | integer | Yes | Maximum possible score (categories x 5) | |
| `categories` | integer | Yes | Number of evaluation categories | |
| `exceptional` | integer | Yes | Score threshold for "fast track" recommendation | ⭐ |
| `strong` | integer | Yes | Score threshold for "recommend interview" | 💪 |
| `acceptable` | integer | Yes | Score threshold for "interview if pipeline allows" | ✅ |
| `borderline` | integer | Yes | Score threshold for "consider for lower level" | ⚠️ |

Candidates scoring below `borderline` are rejected (❌).

### Manager Thresholds

Same structure as IC, but typically:
- 10 categories
- 50 max score
- Higher absolute thresholds due to more categories

### Interview Thresholds

```yaml
thresholds:
  interview:
    max_score: 65
    manager_max_score: 55
    pass: 0.70
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `max_score` | integer | Yes | Maximum IC interview score |
| `manager_max_score` | integer | Yes | Maximum manager interview score |
| `pass` | float | Yes | Minimum percentage to pass (0.0-1.0) |

---

## Paths

Configure directory paths relative to project root.

```yaml
paths:
  rubrics: "rubrics/"
  resumes:
    developers_new: "resumes/developers/new/"
    developers_processed: "resumes/developers/processed/"
    managers_new: "resumes/managers/new/"
    managers_processed: "resumes/managers/processed/"
  evaluations:
    developers: "evaluations/developers/"
    managers: "evaluations/managers/"
```

All paths should be relative to the project root and end with `/`.

---

## Regional Configuration

Enable regional context advisors for candidates from specific regions.

```yaml
regional:
  enabled: true
  contexts:
    india:
      enabled: true
      context_file: "regional/india_context.md"  # Relative to .org/[name]/
      triggers:
        - "india"
        - "bangalore"
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `enabled` | boolean | Yes | Master switch for regional features |
| `contexts.[region].enabled` | boolean | Yes | Enable this specific region |
| `contexts.[region].context_file` | string | Yes | Path relative to `.org/[name]/` |
| `contexts.[region].triggers` | array | Yes | Keywords that trigger this context |

### Creating Regional Context Files

Place context files in `.org/[name]/regional/`. Each file should contain:
- Educational institution rankings and context
- Common resume presentation patterns
- Cultural communication norms
- Interview interpretation guidance

---

## Technology Stack

Define your organization's technology priorities with tags for role-based filtering.

### Tagged Format (Recommended)

```yaml
tech_stack:
  primary:
    - name: ".NET"
      tags: ["backend", "fullstack"]
    - name: "Python"
      tags: ["backend", "data-pipelines", "etl"]
    - name: "React"
      tags: ["frontend", "fullstack"]
  secondary:
    - name: "Spark"
      tags: ["data-pipelines", "big-data"]
    - name: "Docker"
      tags: ["infrastructure", "backend"]
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `primary` | array | No | Core technologies (weighted higher in scoring) |
| `primary[].name` | string | Yes | Technology name |
| `primary[].tags` | array | Yes | Role tags this technology applies to |
| `secondary` | array | No | Acceptable technologies |
| `secondary[].name` | string | Yes | Technology name |
| `secondary[].tags` | array | Yes | Role tags this technology applies to |

### How Tech Stack Filtering Works

When evaluating a candidate:
1. Get the `tech_tags` from the candidate's `role_definitions` entry
2. Filter `tech_stack.primary` to items where any tag matches
3. Filter `tech_stack.secondary` similarly
4. Evaluate candidate against the filtered stack

Example: A `data-engineer` role with `tech_tags: ["data-pipelines", "etl", "sql"]` would match Python, Spark, but not React.

---

## Interview Configuration

```yaml
interview:
  min_questions_per_category: 2
  ticket_jockey_detection: true
  require_specifics: true
```

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `min_questions_per_category` | integer | No | Minimum questions per interview category |
| `ticket_jockey_detection` | boolean | No | Flag candidates who execute but don't lead |
| `require_specifics` | boolean | No | Require concrete examples, not hypotheticals |

---

## Complete Example

See `config.yaml.example` for a complete working example that you can copy and customize.
