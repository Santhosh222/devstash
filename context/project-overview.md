# DevStash — Project Overview

> Version: Draft v0.1
> Status: Planning / Architecture Draft
> Last Updated: June 2026

---

# 🚀 What is DevStash?

DevStash is a developer-focused knowledge vault that centralizes:

- Code snippets
- AI prompts
- Notes
- Commands
- Links
- Files
- Images

into a single searchable workspace.

The goal is to eliminate context switching and reduce the loss of valuable developer knowledge spread across editors, chats, bookmarks, GitHub gists, documents, and terminal history.

---

# 🎯 Core Problem

Developers store information everywhere:

- VS Code snippets
- ChatGPT conversations
- Notion pages
- Browser bookmarks
- GitHub Gists
- README files
- Terminal history
- Random text files

This creates:

- Lost knowledge
- Duplicate work
- Poor discoverability
- Slower onboarding
- Workflow fragmentation

DevStash becomes the "second brain" for developers.

---

# 👥 Target Users

## Everyday Developers

Quick access to snippets, commands and notes.

## AI-First Developers

Store:

- Prompts
- Context files
- Workflows
- System messages

## Content Creators & Educators

Store:

- Course notes
- Explanations
- Tutorials
- Reusable examples

## Full Stack Builders

Store:

- Boilerplates
- API examples
- Design patterns
- Architecture references

---

# 🏗 Product Architecture

```text
┌──────────────────────────┐
│         Frontend         │
│  Next.js 16 + React 19   │
└────────────┬─────────────┘
             │
             ▼
┌──────────────────────────┐
│       API Layer          │
│   Next.js Route APIs     │
└────────────┬─────────────┘
             │
   ┌─────────┼─────────┐
   ▼         ▼         ▼
Postgres    R2      OpenAI
 (Neon)   Storage    GPT-5
   │
 Prisma ORM
```

---

# 📦 System Item Types

| Type | Icon | Color |
|--------|--------|--------|
| Snippet | 💻 | #3b82f6 |
| Prompt | ✨ | #8b5cf6 |
| Command | 🖥️ | #f97316 |
| Note | 📝 | #fde047 |
| File (Pro) | 📄 | #6b7280 |
| Image (Pro) | 🖼️ | #ec4899 |
| Link | 🔗 | #10b981 |

System types are immutable.

Custom types can be added later for Pro users.

---

# 📚 Collections

Collections act as organizational containers.

Examples:

- React Patterns
- Interview Prep
- Context Files
- Python Snippets
- AI Workflows

Key rules:

- One item can belong to many collections
- Collections can contain mixed item types
- Collections can be favorited

---

# 🔍 Search

Searchable fields:

- Title
- Content
- Tags
- Item type

Future enhancements:

- Semantic search
- AI-assisted retrieval
- Search ranking

---

# 🤖 AI Features (Pro)

- Auto-tag generation
- AI summaries
- Explain this code
- Prompt optimization

Recommended future additions:

- Similar item suggestions
- Collection summaries
- Knowledge graph generation

---

# 🔐 Authentication

Supported methods:

- Email / Password
- GitHub OAuth

Implementation:

- NextAuth v5

---

# 💰 Monetization

## Free

- 50 items
- 3 collections
- Basic search
- No uploads
- No AI features

## Pro

- Unlimited items
- Unlimited collections
- File uploads
- Image uploads
- AI features
- Export tools
- Priority support

Pricing:

- $8/month
- $72/year

---

# 🗄 Database Model (Rough Draft)

## User

```prisma
model User {
  id                     String   @id @default(cuid())
  email                  String   @unique
  isPro                  Boolean  @default(false)

  stripeCustomerId       String?
  stripeSubscriptionId   String?

  items                  Item[]
  collections            Collection[]
  itemTypes              ItemType[]

  createdAt              DateTime @default(now())
  updatedAt              DateTime @updatedAt
}
```

## Item

```prisma
model Item {
  id            String   @id @default(cuid())

  title         String
  description   String?

  contentType   String
  content       String?

  fileUrl       String?
  fileName      String?
  fileSize      Int?

  url           String?

  isFavorite    Boolean @default(false)
  isPinned      Boolean @default(false)

  language      String?

  userId        String
  user          User @relation(fields: [userId], references: [id])

  itemTypeId    String
  itemType      ItemType @relation(fields: [itemTypeId], references: [id])

  tags          Tag[]

  collections   ItemCollection[]

  createdAt     DateTime @default(now())
  updatedAt     DateTime @updatedAt
}
```

## ItemType

```prisma
model ItemType {
  id          String  @id @default(cuid())

  name        String
  icon        String
  color       String

  isSystem    Boolean @default(false)

  userId      String?
  user        User? @relation(fields: [userId], references: [id])

  items       Item[]
}
```

## Collection

```prisma
model Collection {
  id             String @id @default(cuid())

  name           String
  description    String?

  isFavorite     Boolean @default(false)

  defaultTypeId  String?

  userId         String
  user           User @relation(fields: [userId], references: [id])

  items          ItemCollection[]

  createdAt      DateTime @default(now())
  updatedAt      DateTime @updatedAt
}
```

## ItemCollection

```prisma
model ItemCollection {
  itemId        String
  collectionId  String

  item          Item @relation(fields: [itemId], references: [id])
  collection    Collection @relation(fields: [collectionId], references: [id])

  addedAt       DateTime @default(now())

  @@id([itemId, collectionId])
}
```

## Tag

```prisma
model Tag {
  id      String @id @default(cuid())
  name    String @unique

  items   Item[]
}
```

---

# 🧭 Core User Flow

```text
Login
  │
  ▼
Dashboard
  │
  ├── Create Collection
  │
  ├── Create Item
  │       │
  │       ├── Snippet
  │       ├── Prompt
  │       ├── Note
  │       ├── Command
  │       ├── Link
  │       └── File
  │
  ▼
Search / Browse
  │
  ▼
Open Drawer
  │
  ▼
Edit / Favorite / Pin
```

---

# 🛠 Recommended Infrastructure

| Layer | Technology |
|---------|------------|
| Frontend | Next.js 16 |
| UI | ShadCN UI |
| Styling | Tailwind CSS v4 |
| ORM | Prisma 7 |
| Database | Neon PostgreSQL |
| Storage | Cloudflare R2 |
| Auth | NextAuth v5 |
| AI | OpenAI GPT-5 Nano |
| Cache | Redis (Optional) |
| Deployment | Vercel |

---

# ⚠️ Development Rules

1. Use Prisma migrations only.
2. Never use `prisma db push` in production workflows.
3. Keep system item types immutable.
4. Build Pro restrictions behind feature flags initially.
5. Design APIs to support future custom item types.
6. Optimize for search speed from the beginning.

---

# 🧱 Suggested MVP Scope

Launch with:

- Authentication
- Item CRUD
- Collections
- Tags
- Favorites
- Search
- Markdown editor
- Dark mode
- File uploads
- GitHub login

Delay until v2:

- AI features
- Custom item types
- Semantic search
- Team workspaces
- Sharing

---

# 🌟 Long-Term Vision

DevStash becomes the developer operating system for personal knowledge:

Code + Prompts + Context + Files + Search + AI

All in one place.
