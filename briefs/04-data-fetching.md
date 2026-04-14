# Module 4: Data Fetching — How the Dashboard Gets Its Data

## Teaching Arc
- **Metaphor:** A librarian with a perfect memory. The database doesn't think — it just retrieves. The fetch functions (in lib/data.ts) are like librarian requests: "get me all invoices where status is 'paid', sorted by date."
- **Opening hook:** When you visit /dashboard, you see charts, tables, numbers. But where does that data come from? It doesn't appear by magic — Server Components call database functions that SQL-query the data and return it.
- **Key insight:** In Next.js App Router, you `await` database calls directly inside Server Components. There's no API layer needed — the component IS the API consumer. The fetch functions use SQL template literals to query PostgreSQL.
- **"Why should I care?":** When AI generates "fetch" code, it often writes client-side fetch calls (fetch('/api/data')). In Next.js App Router, you should fetch in Server Components directly. Understanding data flow helps you catch when AI is doing unnecessary work.

## Code Snippets

File: app/lib/data.ts — fetchRevenue (Server Component database access)
```tsx
import postgres from 'postgres';

const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

export async function fetchRevenue() {
  try {
    const data = await sql<Revenue[]>`SELECT * FROM revenue`;
    return data;
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch revenue data.');
  }
}
```

File: app/lib/data.ts — fetchCardData (parallel queries with Promise.all)
```tsx
export async function fetchCardData() {
  try {
    // Parallel queries — all three start simultaneously, not one after another
    const invoiceCountPromise = sql`SELECT COUNT(*) FROM invoices`;
    const customerCountPromise = sql`SELECT COUNT(*) FROM customers`;
    const invoiceStatusPromise = sql`SELECT
         SUM(CASE WHEN status = 'paid' THEN amount ELSE 0 END) AS "paid",
         SUM(CASE WHEN status = 'pending' THEN amount ELSE 0 END) AS "pending"
         FROM invoices`;

    const data = await Promise.all([
      invoiceCountPromise,
      customerCountPromise,
      invoiceStatusPromise,
    ]);

    const numberOfInvoices = Number(data[0][0].count ?? '0');
    const numberOfCustomers = Number(data[1][0].count ?? '0');
    const totalPaidInvoices = formatCurrency(data[2][0].paid ?? '0');
    const totalPendingInvoices = formatCurrency(data[2][0].pending ?? '0');

    return {
      numberOfCustomers,
      numberOfInvoices,
      totalPaidInvoices,
      totalPendingInvoices,
    };
  } catch (error) {
    console.error('Database Error:', error);
    throw new Error('Failed to fetch card data.');
  }
}
```

File: app/lib/data.ts — fetchFilteredInvoices (parameterized query)
```tsx
const ITEMS_PER_PAGE = 6;
export async function fetchFilteredInvoices(query: string, currentPage: number) {
  const offset = (currentPage - 1) * ITEMS_PER_PAGE;

  try {
    const invoices = await sql<InvoicesTable[]>`
      SELECT
        invoices.id,
        invoices.amount,
        invoices.date,
        invoices.status,
        customers.name,
        customers.email,
        customers.image_url
      FROM invoices
      JOIN customers ON invoices.customer_id = customers.id
      WHERE
        customers.name ILIKE ${`%${query}%`} OR
        customers.email ILIKE ${`%${query}%`} OR
        invoices.amount::text ILIKE ${`%${query}%`} OR
        invoices.date::text ILIKE ${`%${query}%`} OR
        invoices.status ILIKE ${`%${query}%`}
      ORDER BY invoices.date DESC
      LIMIT ${ITEMS_PER_PAGE} OFFSET ${offset}
    `;
    return invoices;
  }
}
```

## Interactive Elements
- [ ] **Code↔English translation** — fetchCardData snippet above (parallel queries)
- [ ] **Data Flow Animation** — showing: Page Component → fetchRevenue() → SQL query → Database → rows → back to Component → HTML sent to browser
- [ ] **Quiz** — 3 questions:
  1. "Why does fetchCardData use Promise.all instead of calling each query one at a time?"
  2. "You're adding a 'total refunds' stat to the dashboard cards. What would you add to the SQL query?"
  3. "Why does the ILIKE query use template literals like ${`%${query}%`} instead of string concatenation?"
- [ ] **Pattern Cards** — showing: SQL template literals, Promise.all parallel fetching, parameterized queries (SQL injection prevention)
- [ ] **Numbered Step Cards** — the data journey from database to displayed page

## Reference Files
- `references/interactive-elements.md` → Code↔English Translation Blocks, Data Flow Animation, Multiple-Choice Quizzes, Pattern Cards, Numbered Step Cards
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** Server vs Client — understood that Server Components run on server and can access databases
- **Next module:** Server Actions — data fetching is read-only; Server Actions let users CREATE and UPDATE data
