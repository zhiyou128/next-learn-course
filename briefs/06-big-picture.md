# Module 6: The Big Picture — How Everything Connects

## Teaching Arc
- **Metaphor:** A city's infrastructure. You don't need to be a civil engineer to appreciate how water flows from reservoir to tap. Similarly, you don't need to write Next.js to understand how a user's click becomes a database update and a new page render. This module zooms out to show the complete system.
- **Opening hook:** Let's trace everything that happens from the moment you visit /dashboard to when you see revenue charts and invoice tables — and then what happens when you click "Create Invoice."
- **Key insight:** Next.js App Router creates a unified system where Server Components fetch data, Server Actions mutate data, and the framework handles routing, caching, and revalidation automatically. It's fewer moving pieces than old architectures.
- **"Why should I care?":** When something breaks — and it will — you need to know WHERE in this flow to look. Debugging is about tracing backwards from symptom to cause. Knowing the full architecture is what makes you effective at steering AI through problems.

## Code Snippets

File: app/page.tsx — the dashboard home (entry point)
```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';

export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      {/* ... */}
      <Link href="/login" className="...">
        <span>Log in</span> <ArrowRightIcon className="w-5" />
      </Link>
    </main>
  );
}
```

File: app/ui/dashboard/sidenav.tsx — navigation, no 'use client'
```tsx
import Link from 'next/link';
import NavLinks from '@/app/ui/dashboard/nav-links';
// ...
export default function SideNav() {
  return (
    <div className="...">
      <Link href="/">...</Link>
      <NavLinks />
      <form>...</form>
    </div>
  );
}
```

File: app/lib/actions.ts — complete server action with full flow
```tsx
'use server';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

export async function createInvoice(prevState: State, formData: FormData) {
  // validate → insert → revalidatePath → redirect
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

## Architecture Summary (from code analysis)
- **app/** — Routes: URL paths are defined by folder structure
  - **layout.tsx** — Wraps all pages, provides shared UI (sidenav)
  - **page.tsx** — Renders for a specific URL
  - **lib/** — Backstage: data fetching, utilities, actions
  - **ui/** — Costume shop: reusable visual components
- **Server Components** — Default. Fetch data, access DB directly, return JSX
- **Client Components** — Marked with 'use client'. For interactivity only
- **Server Actions** — Marked with 'use server'. Handle form submissions and mutations
- **revalidatePath** — Clears Next.js cache for a route, forcing fresh data on next visit
- **redirect** — Sends user to a new page after a server action completes

## Interactive Elements
- [ ] **Architecture Diagram** — Full system diagram showing: Browser → Next.js Server → Server Components → Database. And: Form Submit → Server Action → DB Write → revalidatePath → redirect
- [ ] **Quiz** — 4 scenario questions:
  1. "A user reports that the dashboard shows stale data even after new invoices are added. What's likely wrong?"
  2. "You want to add a 'Mark as Paid' button next to each invoice. What files would you need to touch?"
  3. "Why is there no /api/invoices route in this project?"
  4. "A component needs to display a countdown timer. Does it run on server or client?"
- [ ] **Data Flow Animation** — Complete flow: visit /dashboard → layout renders → page calls fetchRevenue() → SQL → returns data → Next.js sends HTML → user clicks "Create Invoice" → form action → server action → validate → insert → revalidatePath → redirect
- [ ] **Callout Box** — "The key mental model: In Next.js App Router, the line between 'frontend' and 'backend' is blurred. Server Components ARE the API — no separate API routes needed for most cases."
- [ ] **Pattern Cards** — Summary of all patterns learned: file-based routing, server components, client components, server actions, SQL template literals, revalidatePath

## Reference Files
- `references/interactive-elements.md` → Interactive Architecture Diagram, Data Flow Animation, Multiple-Choice Quizzes, Callout Boxes, Pattern Cards
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** Server Actions — the last major concept
- **Next module:** Course complete — this ties all modules together into a coherent whole
