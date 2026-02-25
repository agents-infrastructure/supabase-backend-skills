# Supabase Backend Skills

用于 AI Agent 的 Supabase 后端技能集合，基于官方最佳实践整理，覆盖数据库、RLS、Edge Functions、Realtime 与认证场景。

## 功能覆盖

- 数据库 SQL 风格与规范
- 数据库迁移（Migration）
- PostgreSQL 函数设计
- Row Level Security (RLS) 策略
- 声明式 Schema 管理
- Supabase Edge Functions（TypeScript）
- Realtime（broadcast / presence）
- Next.js 与 Expo 的 Supabase Auth

## 安装指南

### 1. 通过 Skills CLI 安装（推荐）

```bash
npx skills add agents-infrastructure/supabase-backend-skills
```

安装完成后，重启你的 AI Agent（如 Codex / Claude Code / Cursor）以加载新技能。

### 2. 本地方式安装（开发/调试）

1. 克隆仓库：

```bash
git clone git@github.com:agents-infrastructure/supabase-backend-skills.git
cd supabase-backend-skills
```

2. 复制或软链接到你的技能目录（按你的 Agent 选择其一）：

```bash
# Codex
mkdir -p ~/.codex/skills
ln -s "$(pwd)" ~/.codex/skills/supabase-backend-skills

# Agents 平台（如使用 ~/.agents/skills）
mkdir -p ~/.agents/skills
ln -s "$(pwd)" ~/.agents/skills/supabase-backend-skills
```

## 使用指南

### 触发方式

当用户提出 Supabase 相关需求时触发本技能，例如：

- “帮我写一个 Supabase RLS policy”
- “给我一个数据库 migration 脚本”
- “写一个 Supabase Edge Function”
- “Next.js 里怎么做 Supabase Auth”

### 推荐工作流

1. 根据任务类型定位对应参考文件。
2. 先读取 `references/*.md` 中的约束与示例。
3. 再生成 SQL / TypeScript / 配置，确保遵循该文件中的安全与性能规则。

### 任务与参考文件映射

| 任务领域 | 参考文件 |
| --- | --- |
| SQL 风格规范 | `references/code-format-sql.md` |
| 数据库迁移 | `references/database-create-migration.md` |
| 数据库函数 | `references/database-functions.md` |
| RLS 策略 | `references/database-rls-policies.md` |
| 声明式 Schema | `references/declarative-database-schema.md` |
| Edge Functions | `references/edge-functions.md` |
| Realtime | `references/use-realtime.md` |
| Next.js Auth | `references/nextjs-supabase-auth.md` |
| Expo Auth | `references/expo-supabase-auth.md` |

## 项目结构

```text
supabase-backend-skills/
├── README.md
├── SKILL.md
└── references/
    ├── code-format-sql.md
    ├── database-create-migration.md
    ├── database-functions.md
    ├── database-rls-policies.md
    ├── declarative-database-schema.md
    ├── edge-functions.md
    ├── expo-supabase-auth.md
    ├── nextjs-supabase-auth.md
    └── use-realtime.md
```
