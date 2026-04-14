# Module 3: Server vs Client — Where Does Code Run?

## Teaching Arc
- **Metaphor:** A restaurant kitchen. The "front of house" (client) is what the diner sees — menus, tables, waiters. The "back of house" (server) is where the cooking happens. In Next.js, some code runs in the kitchen (server), some runs at your table (client browser). Putting the wrong code in the wrong place causes problems.
- **Opening hook:** When you click a button on a webpage, something has to decide what happens. Does that decision get made on the server (safely, with access to databases) or on your browser (quickly, with direct user interaction)?
- **Key insight:** By default, all components in Next.js App Router are Server Components — they run on the server and their code never reaches the browser. Only add 'use client' when you need interactivity (click handlers, state, browser APIs).
- **"Why should I care?":** AI often doesn't know where to put code. If you ask for "a form that submits data" and AI puts it all in a Server Component, it won't work. Understanding server vs client is the difference between code that works and code that silently fails.

## Code Snippets

File: app/ui/dashboard/revenue-chart.tsx (Server Component — no 'use client')
```tsx
// No 'use client' directive = this runs on the server
// It can directly call database functions and pass data to the UI
export async function RevenueChart() {
  // This runs on the server — database access is direct
  const data = await fetchRevenue();
  // ...
}
```

File: app/ui/login-form.tsx (Client Component — has interactivity)
```tsx
'use client';
// 'use client' means this runs in the browser
// Use it when you need: useState, useEffect, event handlers, browser APIs

import { lusitana } from '@/app/ui/fonts';
import {
  AtSymbolIcon,
  KeyIcon,
  ExclamationCircleIcon,
} from '@heroicons/react/24/outline';

export default function LoginForm() {
  // useState and event handlers can only exist in 'use client' components
  return (
    <form className="space-y-3">
      {/* form JSX with event handlers */}
    </form>
  );
}
```

File: app/ui/dashboard/sidenav.tsx — note it has NO 'use client' but has interactive elements (buttons)

## Interactive Elements
- [ ] **Code↔English translation** — login-form.tsx snippet above, showing the 'use client' directive
- [ ] **Architecture Diagram** — showing server-side vs client-side execution, with actors: Server (database, business logic), Browser (UI, interactivity)
- [ ] **Quiz** — 3 scenario questions:
  1. "You need a component that shows a loading spinner while data loads. Client or Server?"
  2. "A component reads from a database to display customer names. Where should that logic live?"
  3. "You want a button that changes color when clicked. Does it need 'use client'?"
- [ ] **Group Chat Animation** — Server Component and Client Component arguing about who should handle a click event
- [ ] **Layer Toggle** — showing the difference: what the server sends vs what the browser runs

## Reference Files
- `references/interactive-elements.md` → Code↔English Translation Blocks, Group Chat Animation, Layer Toggle Demo, Multiple-Choice Quizzes
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** Meet the Cast — knew which files existed but not where they run
- **Next module:** Data Fetching — now that we know some code runs on server, we can dive into HOW server code fetches data
