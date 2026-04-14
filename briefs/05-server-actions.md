# Module 5: Server Actions — Making Things Happen

## Teaching Arc
- **Metaphor:** A suggestion box at a restaurant. A customer fills out a form (client sends data), and someone behind the scenes reads it and updates the reservation book (server action processes it). The customer doesn't watch the kitchen work — they just get a result.
- **Opening hook:** Fetching data is half the story. The other half is: what happens when a user creates an invoice, edits a customer, or deletes a record? In Next.js, that's what Server Actions handle.
- **Key insight:** Server Actions are functions marked with 'use server' that run on the backend. They receive form data, validate it, write to the database, and can redirect the user or revalidate cached pages. They're the bridge between user interaction and database mutation.
- **"Why should I care?":** AI often writes separate API routes + fetch calls for mutations. In Next.js App Router, Server Actions are the idiomatic way — simpler code, less network overhead. Knowing this helps you guide AI toward the right pattern.

## Code Snippets

File: app/lib/actions.ts — createInvoice server action
```tsx
'use server';

import { z } from 'zod';
import postgres from 'postgres';
import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';

const sql = postgres(process.env.POSTGRES_URL!, { ssl: 'require' });

const FormSchema = z.object({
  id: z.string(),
  customerId: z.string({
    invalid_type_error: 'Please select a customer.',
  }),
  amount: z.coerce
    .number()
    .gt(0, { message: 'Please enter an amount greater than $0.' }),
  status: z.enum(['pending', 'paid'], {
    invalid_type_error: 'Please select an invoice status.',
  }),
  date: z.string(),
});

const CreateInvoice = FormSchema.omit({ id: true, date: true });

export type State = {
  errors?: {
    customerId?: string[];
    amount?: string[];
    status?: string[];
  };
  message?: string | null;
};

export async function createInvoice(prevState: State, formData: FormData) {
  // 1. Validate form fields using Zod
  const validatedFields = CreateInvoice.safeParse({
    customerId: formData.get('customerId'),
    amount: formData.get('amount'),
    status: formData.get('status'),
  });

  // If form validation fails, return errors early
  if (!validatedFields.success) {
    return {
      errors: validatedFields.error.flatten().fieldErrors,
      message: 'Missing Fields. Failed to Create Invoice.',
    };
  }

  // 2. Prepare data
  const { customerId, amount, status } = validatedFields.data;
  const amountInCents = amount * 100;
  const date = new Date().toISOString().split('T')[0];

  // 3. Insert into database
  try {
    await sql`
      INSERT INTO invoices (customer_id, amount, status, date)
      VALUES (${customerId}, ${amountInCents}, ${status}, ${date})
    `;
  } catch (error) {
    return {
      message: 'Database Error: Failed to Create Invoice.',
    };
  }

  // 4. Revalidate cache and redirect
  revalidatePath('/dashboard/invoices');
  redirect('/dashboard/invoices');
}
```

File: app/ui/invoices/create-form.tsx — form that calls the server action
```tsx
'use client';
// Form component (client) calls server action (server)

export default function CreateForm() {
  const [state, formAction] = useFormState(createInvoice, {});
  // or with react-hook-form + action={createInvoice}
  return (
    <form action={formAction}>
      {/* form fields */}
    </form>
  );
}
```

## Interactive Elements
- [ ] **Code↔English translation** — createInvoice snippet above (full function)
- [ ] **Data Flow Animation** — showing: User fills form → formAction calls server → server validates → server writes to DB → revalidatePath clears cache → redirect to /dashboard/invoices
- [ ] **Quiz** — 3 scenario questions:
  1. "A user submits an invoice form but enters '-50' as the amount. Where is this caught and how?"
  2. "After creating an invoice, why does the code call revalidatePath instead of just returning?"
  3. "You're building a 'delete invoice' feature. What would a delete server action need to do?"
- [ ] **Group Chat Animation** — Form says "I have invoice data!" → Server Action says "Let me validate it!" → Database says "Done!" → Server Action says "Cache cleared!" → Browser redirects
- [ ] **Callout Box** — explaining why 'use server' at top of file vs inline in form action

## Reference Files
- `references/interactive-elements.md` → Code↔English Translation Blocks, Data Flow Animation, Group Chat Animation, Multiple-Choice Quizzes, Callout Boxes
- `references/content-philosophy.md` → (always)
- `references/gotchas.md` → (always)

## Connections
- **Previous module:** Data Fetching — understood read operations; Server Actions are the write/mutate counterpart
- **Next module:** The Big Picture — tying everything together into the complete architecture
