# Module 1: What is Next.js? — App Router Architecture

## Teaching Arc
- **Metaphor:** A shopping mall directory vs individual store maps. Next.js is the mall directory that tells visitors where everything is and how to get there — without it, they'd wander lost through corridors of code.
- **Opening hook:** You've used web apps your whole life. But have you ever wondered what actually happens when you type a URL and see a page appear? Next.js is the framework that sits between "user wants to see something" and "user sees it."
- **Key insight:** Next.js uses the filesystem as a map — folder names become URL paths automatically. No configuration needed.
- **"Why should I care?":** When you tell an AI "add a login page," you need to know WHERE in the project structure it goes. Next.js has rules about this. Understanding the app/ folder structure is the first step to knowing how to direct AI effectively.

## Code Snippets

File: app/page.tsx (the homepage)
```tsx
import AcmeLogo from '@/app/ui/acme-logo';
import { ArrowRightIcon } from '@heroicons/react/24/outline';
import Link from 'next/link';

export default function Page() {
  return (
    <main className="flex min-h-screen flex-col p-6">
      <div className="flex h-20 shrink-0 items-end rounded-lg bg-blue-500 p-4 md:h-52">
        {/* <AcmeLogo /> */}
      </div>
      <div className="mt-4 flex grow flex-col gap-4 md:flex-row">
        <div className="flex flex-col justify-center gap-6 rounded-lg bg-gray-50 px-6 py-10 md:w-2/5 md:px-20">
          <p className={`text-xl text-gray-800 md:text-3xl md:leading-normal`}>
            <strong>Welcome to Acme.</strong> This is the example for the{' '}
            <a href="https://nextjs.org/learn/" className="text-blue-500">
              Next.js Learn Course
            </a>
            , brought to you by Vercel.
          </p>
          <Link
            href="/login"
            className="flex items-center gap-5 self-start rounded-lg bg-blue-500 px-6 py-3 text-sm font-medium text-white transition-colors hover:bg-blue-400 md:text-base"
          >
            <span>Log in</span> <ArrowRightIcon className="w-5 md:w-6" />
          </Link>
        </div>
        <div className="flex items-center justify-center p-6 md:w-3/5 md:px-28 md:py-12">
          {/* Add Hero Images Here */}
        </div>
      </div>
    </main>
  );
}
```

File: app/layout.tsx (the root layout)
```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

File: app/ui/dashboard/sidenav.tsx (navigation component)
```tsx
import Link from 'next/link';
import NavLinks from '@/app/ui/dashboard/nav-links';
import AcmeLogo from '@/app/ui/acme-logo';
import { PowerIcon } from '@heroicons/react/24/outline';

export default function SideNav() {
  return (
    <div className="flex h-full flex-col px-3 py-4 md:px-2">
      <Link
        className="mb-2 flex h-20 items-end justify-start rounded-md bg-blue-600 p-4 md:h-40"
        href="/"
      >
        <div className="w-32 text-white md:w-40">
          <AcmeLogo />
        </div>
      </Link>
      <div className="flex grow flex-row justify-between space-x-2 md:flex-col md:space-x-0 md:space-y-2">
        <NavLinks />
        <div className="hidden h-auto w-full grow rounded-md bg-gray-50 md:block"></div>
        <form>
          <button className="flex h-[48px] w-full grow items-center justify-center gap-2 rounded-md bg-gray-50 p-3 text-sm font-medium hover:bg-sky-100 hover:text-blue-600 md:flex-none md:justify-start md:p-2 md:px-3">
            <PowerIcon className="w-6" />
            <div className="hidden md:block">Sign Out</div>
          </button>
        </form>
      </div>
    </div>
  );
}
```

## Interactive Elements
- [ ] **Code↔English translation** — app/page.tsx snippet above
- [ ] **Group Chat Animation** — actors: User, Browser, Next.js Server, Database. Message: User types URL → Browser asks Next.js → Server asks Database → Database sends data → Server sends HTML → Browser shows page
- [ ] **Quiz** — 3 questions, architecture/decisions style:
  1. "You're adding a 'Contact Us' page. Which file do you create?" (scenario: where does it go)
  2. "Why does Next.js use a folder-based system for routing?"
  3. "What's the difference between app/layout.tsx and app/page.tsx?"
- [ ] **Visual File Tree** — show the app/ folder structure with annotations
- [ ] **Data Flow Animation** — showing URL → Next.js → page component → browser

## Reference Files
- `references/interactive-elements.md` → Group Chat Animation, Data Flow Animation, Code↔English Translation Blocks, Visual File Tree, Multiple-Choice Quizzes, Glossary Tooltips
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** None — this is the introduction
- **Next module:** Meet the Cast — we drill into each file/folder type introduced here
