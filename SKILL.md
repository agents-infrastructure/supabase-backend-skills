---
name: supabase-backend-skills
description: Provides a collection of official Supabase AI prompts for backend development. Use when working with Supabase for tasks involving database management (schemas, migrations, functions, RLS), authentication, Edge Functions, and Realtime features. This skill helps generate high-quality, best-practice code and configurations for Supabase projects.
---

# Supabase Backend Skills

This skill provides a collection of curated, official prompts for working with Supabase using AI-powered IDE tools. These prompts are designed to help you generate high-quality, best-practice code and configurations for various Supabase features.

## How to Use

When a user asks for help with a Supabase-related task, identify the relevant area (e.g., database functions, RLS, Edge Functions) and load the corresponding prompt from the `references/` directory into your context. Use the guidelines within the prompt to assist the user or generate code.

For example, if the user wants to create a new database function, you should read the contents of `references/database-functions.md` before generating the SQL code.

## Available Prompts

The following prompts are available in the `references/` directory. Refer to the specific prompt file for detailed instructions, do's and don'ts, and code examples related to the task at hand.

| Task Area | File Name | Description |
| --- | --- | --- |
| **Database** | | |
| SQL Style Guide | `code-format-sql.md` | Provides a comprehensive style guide for writing clean and consistent Postgres SQL. |
| Database Migrations | `database-create-migration.md` | Guidelines for creating secure and reliable database migration files using the Supabase CLI. |
| Database Functions | `database-functions.md` | Best practices and templates for writing secure and efficient PostgreSQL functions. |
| RLS Policies | `database-rls-policies.md` | A detailed guide on creating Row-Level Security (RLS) policies, including performance tips and helper functions. |
| Declarative Schema | `declarative-database-schema.md` | Instructions for managing your database schema declaratively using `.sql` files. |
| **Backend Logic** | | |
| Edge Functions | `edge-functions.md` | Rules and patterns for writing high-quality Supabase Edge Functions in TypeScript. |
| **Realtime** | | |
| Realtime Features | `use-realtime.md` | An expert guide to implementing Supabase Realtime, covering `broadcast`, `presence`, and scalability best practices. |
| **Authentication** | | |
| Next.js Auth | `nextjs-supabase-auth.md` | Critical instructions for correctly implementing Supabase Auth in a Next.js v16 application. |
| Expo React Native Auth | `expo-supabase-auth.md` | Guidelines for implementing Supabase Auth in Expo React Native apps, covering SecureStore, OAuth deep linking, and native Google/Apple sign-in. |
