# Video Scheduler (ORBIT)

## Overview

A social media video scheduling tool that allows users to schedule automatic daily video posts across Facebook, TikTok, and YouTube from a single dashboard. Built as a pnpm workspace monorepo using TypeScript.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **Frontend**: React + Vite + Tailwind CSS + shadcn/ui
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)

## Features

- **Dashboard** — Overview of upcoming posts, recent activity, platform stats, summary cards
- **Video Library** — Manage uploaded videos, add new videos with tags and metadata
- **Schedules** — Create and manage posting schedules, filter by status/platform
- **Platforms** — Manage connected accounts (Facebook, TikTok, YouTube), toggle active/inactive
- **Settings** — API configuration guidance for each platform

## Database Tables

- `videos` — Video metadata (title, description, file path, tags, duration)
- `platforms` — Connected social media accounts
- `schedules` — Scheduled posts linking videos to platforms with timestamps
- `activity` — Activity feed log

## Key Commands

- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- `pnpm --filter @workspace/api-server run dev` — run API server locally

## API Endpoints

- `GET/POST /api/videos` — List/create videos
- `GET/PATCH/DELETE /api/videos/:id` — Get/update/delete video
- `GET/POST /api/schedules` — List/create schedules
- `GET/PATCH/DELETE /api/schedules/:id` — Get/update/delete schedule
- `GET/POST /api/platforms` — List/create platform accounts
- `PATCH/DELETE /api/platforms/:id` — Update/delete platform
- `GET /api/dashboard/summary` — Dashboard summary stats
- `GET /api/dashboard/recent-activity` — Recent activity feed
- `GET /api/dashboard/platform-stats` — Stats per platform
- `GET /api/dashboard/upcoming` — Upcoming scheduled posts

See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details.
