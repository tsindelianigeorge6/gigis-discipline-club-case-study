# Gigi's Discipline Club

A Georgian-language fitness-coaching membership platform built around one coach's 90-day discipline system.

**Status:** live in production · built 2026-06 → 2026-10 · handed over to the client

**Live site:** https://www.gigisdisciplineclub.com

> The source code is private and proprietary and belongs to the client. This repository contains documentation only: no code, no configuration, no data. All rights reserved; nothing here is licensed for reuse.

## What it is

A new member answers a questionnaire and gets a workout program and, on packages that include nutrition, a menu assigned from the answers. The coach reviews flagged cases and assigns by hand where no library program fits. Members train with exercise videos, log their sets and send weekly check-ins; the coach answers from an admin panel. Membership is paid, by monthly subscription or a one-off package, with real payments since 2026-09-30.

## What it does

**Members**

- Onboarding questionnaire; a program and a menu assigned from the answers
- Workouts logged set by set, with exercise videos; days and exercises can be put in the member's own order
- Automatic menu, food diary, weight log
- Weekly check-ins with coach feedback; a community space
- Billing history and self-service cancellation

**Coach**

- A daily queue: programs to review, check-ins to answer, payments needing attention
- Program builder and exercise library with video upload
- Per-member personal menus and personal program copies
- Leads, follow-up tasks, community moderation

**Business**

- Subscriptions and a one-off package through Flitt, a Georgian payment provider
- A dedicated landing page for ad traffic, with its own questionnaire
- Meta Pixel and Conversions API, with a visitor opt-out and no health or contact data sent

## My role

I was the product owner and builder. I did not write the code by hand: AI coding agents wrote it (Claude Code, and for a period OpenAI Codex) under my direction, change by change. I defined the product with the coach, wrote the requirements, made the product and trade-off decisions, reviewed results and tested on real devices. Every production database change and release went out on my decision and approval; from late September on I ran the database changes by hand myself. I operated the live service through launch.

## Stack

- Next.js 16 (App Router), React 19, TypeScript, Tailwind CSS v4
- Supabase: Postgres with row-level security, auth, storage
- Vercel: hosting, scheduled jobs
- Flitt: payments, subscriptions
- Meta Pixel, Conversions API
- GitHub Actions CI

## How it was built

- **Migrations first.** Schema changes are numbered migrations, replayed from zero on a fresh local Postgres and applied by hand before the code that needs them. Since the pre-launch hardening, almost every migration carries its own pre- and post-checks.
- **Rehearsals.** One-off data changes are separate scripts, usually with check and rollback files and a rehearsal in a disposable database before production.
- **Written releases.** Releases ship with an owner-facing document: the steps, the rollback, what was tested and what was not.
- **CI on every pull request.** Audit, lint, typecheck, tests, build and a strict Content-Security-Policy test.
- **Security.** Row-level security, server-verified payment callbacks, least-privilege database functions.

## Selected problems solved

**Moving the production database.** The database moved to a region closer to its users in Georgia. After a rehearsal, the cutover took about 18 minutes of planned downtime; row counts were compared on both sides, and a way back was ready for the cutover window.

**From sandbox to live money.** Access opens only on a payment confirmation the server has verified, never on a browser redirect. The switch to live payments followed a sequence that was rehearsed locally first, and the rehearsal deliberately ran the steps in the wrong order to see what each mistake would break.

**A nutrition planner with versioned rules.** Automatic menus are computed from a member's answers, and each rule change ships as a new ruleset version. Before release, its menus are compared with the previous version's for each of 3,000 synthetic members. Nothing the coach wrote by hand is changed.

**Exercise alternatives.** A program can list two interchangeable exercises for one slot. Members pick one before or during a workout, and completed sets are never altered. The swap was tested with real parallel database sessions, and a deadlock found in review was fixed.

**Personal program copies.** One button gives a member a personal copy of a shared program for the coach to edit; the shared program stays untouched. A companion rule replaces knee-loading exercises for members whose questionnaire answers call for it. It touches automatic programs only, never a program the coach wrote by hand, and is turned on by an explicit switch.

**Members arrange their own week.** Members can reorder their training days and the exercises inside a day. The order is saved for that member and that program only, the coach's numbering stays visible, and the feature shipped without a database change.

## By the numbers

| Measure | At handover |
|---|---|
| Built | 2026-06-07 to 2026-10-08 |
| Commits on main | 456 |
| Database migrations | 84 |
| Test files | 146, about 2,300 test cases |
| Test scripts chained in CI | 119 |
| TypeScript source | about 92,000 lines in 616 files |
| Test code | about 72,000 lines |
| SQL migrations | about 29,000 lines |

## Status and rights

Handed over on 2026-10-08: the code, hosting and data now belong to the client. The source code is private and proprietary. This repository contains documentation only. All rights reserved; nothing here is licensed for reuse.

Product, direction and operations by George; code written by AI coding agents under his direction.
