# Pathfinder AI

Pathfinder AI is a decision-support tool we built at IC Hack 2025, Imperial College London's student hackathon. You describe a decision or situation you're weighing up, and the app uses Claude to help you explore it as a branching tree: it asks a few clarifying questions, then generates a handful of likely future scenarios based on your answers, and you can keep drilling into any of those branches the same way.

## How it works

You start by typing a decision on the home page (e.g. "I'm planning to found a startup") or picking one of the example prompts. This creates a root node in the tree.

For the current node, the app asks Claude for two to four short clarifying questions about the situation, using the whole path of scenarios and previous answers as context so it doesn't repeat itself. You answer them in a set of cards next to the tree.

Once you submit your answers, the app calls Claude again to generate two to four likely future scenarios that branch out from the current node. These appear as new nodes underneath the one you're on. You can click into any of them to explore that branch further (new questions, new sub-scenarios), or use the arrow and breadcrumb navigation to go back up the tree.

Everything is persisted in Postgres: scenarios and questions are stored as rows referencing each other by UUID, and a recursive history function walks back up the parent chain to rebuild the whole scenario history sent to Claude on each call.

## Tech stack

- Next.js 15 (App Router, Turbopack for dev) with React 19 and TypeScript
- Tailwind CSS with shadcn/ui components (built on Radix primitives)
- react-hook-form and zod for form validation
- Anthropic SDK, calling Claude 3.5 Sonnet for the questions and scenarios
- postgres.js talking directly to a Postgres database, no ORM

## Running locally

Install dependencies and start the dev server:

```bash
bun install
bun dev
```

(npm, yarn, or pnpm work too, it's a standard Next.js project.)

You'll need two environment variables:

- `CLAUDE_API_KEY`, an Anthropic API key
- `POSTGRES_URL`, a connection string for a Postgres database

The database needs two tables, `scenarios` and `questions`; the schema we used is in a comment at the top of `src/lib/db.ts`.

Then open http://localhost:3000.

## Repository layout

- `src/app/page.tsx` - home page, where you start a new decision
- `src/app/[id]/page.tsx` - the tree view for a given scenario node
- `src/components/` - UI components (tree nodes, question forms, breadcrumbs), with `src/components/ui/` holding the shadcn/ui primitives
- `src/lib/claude.ts` - prompts and calls to Claude for generating questions and scenarios
- `src/lib/db.ts` - all the Postgres queries and the recursive history logic
- `src/lib/postgres.ts` - database connection setup

## License

No license is included, so all rights are reserved by default.
