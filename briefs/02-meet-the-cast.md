# Module 2: Meet the Cast — Components and Files

## Teaching Arc
- **Metaphor:** A theater production. Every file is an actor with a specific role. `page.tsx` is the lead actor who appears on stage for each URL. `layout.tsx` is the stage manager who ensures the set looks consistent across scenes. The `ui/` folder is the costume shop — reusable pieces that any page can borrow.
- **Opening hook:** When you look at a Next.js project, you're looking at a cast of files. Each one has a specific job. Know the cast, and you know where to ask AI to put things.
- **Key insight:** Next.js conventions assign specific roles to files — `page.tsx` always renders for a URL, `layout.tsx` wraps a section with shared UI, files in `lib/` hold shared logic, and `ui/` holds reusable visual components.
- **"Why should I care?":** When AI produces code, it often puts things in the wrong "room." Knowing which file does what means you can say "no, that's a UI component — put it in ui/, not page.tsx."

## Code Snippets

File: app/ui/dashboard/nav-links.tsx (reusable navigation links)
```tsx
import {
  UserGroupIcon,
  HomeIcon,
  DocumentDuplicateIcon,
} from '@heroicons/react/24/outline';

const links = [
  { name: 'Home', href: '/dashboard', icon: HomeIcon },
  {
    name: 'Invoices',
    href: '/dashboard/invoices',
    icon: DocumentDuplicateIcon,
  },
  { name: 'Customers', href: '/dashboard/customers', icon: UserGroupIcon },
];

export default function NavLinks() {
  return (
    <>
      {links.map((link) => {
        const LinkIcon = link.icon;
        return (
          <a
            key={link.name}
            href={link.href}
            className="flex h-[48px] grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3"
          >
            <LinkIcon className="w-6" />
            <p className="hidden md:block">{link.name}</p>
          </a>
        );
      })}
    </>
  );
}
```

File: app/ui/button.tsx (reusable UI component)
```tsx
import { ReactNode } from 'react';

export function Button({ children }: { children: ReactNode }) {
  return (
    <button className="rounded-md bg-blue-500 px-4 py-2 text-sm font-medium text-white hover:bg-blue-400 transition-colors">
      {children}
    </button>
  );
}
```

File: app/lib/utils.ts (shared utility functions)
```tsx
export const formatCurrency = (amount: number) => {
  return (amount / 100).toLocaleString('en-US', {
    style: 'currency',
    currency: 'USD',
  });
};
```

## Interactive Elements
- [ ] **Code↔English translation** — nav-links.tsx snippet above
- [ ] **Pattern/Feature Cards** — showing the roles: page (actor), layout (stage manager), ui/ (costume shop), lib/ (backstage crew)
- [ ] **Quiz** — 3 scenario questions:
  1. "You need a dropdown menu that appears on multiple pages. Where do you put it?"
  2. "A function formats dollar amounts consistently across the app. Where should it live?"
  3. "You're building a page that shows invoice history. Which file handles the URL /dashboard/invoices?"
- [ ] **Icon-Label Rows** — showing each main directory and what it does
- [ ] **Group Chat Animation** — between different components: "Hey I'm a button, reuse me!" "I'm a page, I need a button!"

## Reference Files
- `references/interactive-elements.md` → Pattern/Feature Cards, Icon-Label Rows, Code↔English Translation Blocks, Multiple-Choice Quizzes, Group Chat Animation
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** What is Next.js? — introduced the folder structure
- **Next module:** Server vs Client — we introduce that some components run on server, some on client
