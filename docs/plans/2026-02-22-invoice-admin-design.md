# Black Frog Built — Invoice & Business Admin Platform

## Status: Approved Design (Phase 1 ready to build)

## Vision
Start with an invoice management system, grow into a full business ops hub, and eventually offer as a SaaS product for freelancers and agencies.

## Key Decisions

| Decision | Choice |
|----------|--------|
| Access | Admin + Client Portal + Future Team Support |
| Location | Separate app (e.g., admin.blackfrogbuilt.com or app domain) |
| Tech stack | Next.js 14+ App Router + Supabase + Tailwind CSS + Vercel |
| Architecture | Multi-tenant ready from day one (SaaS potential) |
| Invoice template | SVG generation server-side using locked JS template |
| Marketing site | Stays static on GitHub Pages (blackfrogbuilt.com) — untouched |

## Tech Stack: Next.js + Supabase (Approach A)

### Why This Stack
- **Supabase** provides auth, PostgreSQL database, realtime subscriptions, and file storage out of the box
- **PostgreSQL** — relational data (invoices → clients → line items → payments) needs SQL, not NoSQL
- **Next.js App Router** — server components, API routes, middleware for auth guards
- **Tailwind CSS** — rapid styling, matches the terminal-dark brand aesthetic
- **Vercel** — push to GitHub, auto-deploy. Free tier covers early usage
- **TypeScript** throughout — type safety for a product that will grow
- **Multi-tenant architecture** from the start via Supabase Row Level Security

### Alternatives Considered
- **Firebase (Approach B):** Fastest setup but Firestore (NoSQL) gets painful for relational invoice/client data. More vendor lock-in.
- **Full Custom Express + React + PostgreSQL (Approach C):** Maximum control but weeks of boilerplate (auth, sessions, storage, deployment) before touching invoices.

## Growth Roadmap

### Phase 1: Invoice System (BUILD THIS FIRST)
- Create, edit, preview, and send invoices
- SVG → PDF generation using the locked branded template
- Client management (name, company, address, email)
- Invoice tracking (draft, sent, viewed, paid, overdue)
- Dashboard with invoice stats
- Admin authentication (Supabase Auth)

### Phase 2: Client Portal
- Clients log in to view their invoices
- Download PDF invoices
- Payment status visibility
- Role-based access (admin vs client)

### Phase 3: Business Ops Hub
- Project tracking tied to invoices
- Proposals / estimates (reuse invoice template)
- Time tracking
- Recurring invoices
- Expense tracking
- Reporting / analytics dashboard

### Phase 4: SaaS Product
- Multi-tenant (other freelancers/agencies sign up)
- Custom branding per tenant (their logo, colors, domain)
- Subscription billing (Stripe)
- Onboarding flow
- Public marketing/pricing page

## Brand & Design Specs

### Colors (match main site + invoice template)
- Dark: `#030712`
- Lime: `#A3E635`
- White: `#F3F4F6`
- Gray: `#6B7280`
- Gray Dark: `#374151`
- Gray Light: `#9CA3AF`

### Typography
- **Primary:** JetBrains Mono (Google Fonts) — headings, UI elements
- **Invoice template:** Consolas, Monaco, monospace (system fonts)
- **Aesthetic:** Terminal-accented modern — dark theme, monospace, code-style accents

### UI Direction
- Dark-themed admin (matches brand)
- Terminal/code aesthetic carried over from the marketing site
- Clean data tables, card-based layouts
- Lime green for CTAs and status indicators

## Invoice Template System

### Current State (locked in)
- **Template:** `collateral/invoice-template-locked.js` — parameterized Node.js SVG generator
- **Data model:** `INVOICE_DATA` object with from, billTo, date, terms, project, items[], discount
- **Output:** SVG file (letter size 612x792)
- **Logo:** Base64-embedded frog (inverted filter, 150x150 cropped)

### Admin Integration Plan
- Template logic moves into a Next.js API route (`/api/invoices/generate`)
- Invoice data stored in Supabase PostgreSQL
- SVG generated on-demand from stored data
- PDF export via server-side SVG → PDF conversion (puppeteer or similar)
- Preview in-browser before sending

## Database Schema (Phase 1 - Preliminary)

```sql
-- Clients
clients (
  id uuid PK,
  name text,
  company text,
  email text,
  phone text,
  address_line1 text,
  address_line2 text,
  created_at timestamptz
)

-- Invoices
invoices (
  id uuid PK,
  number text UNIQUE,        -- e.g., INV-2026-001
  client_id uuid FK → clients,
  project_name text,
  date date,
  terms text,                -- e.g., "Due on Receipt"
  status text,               -- draft, sent, viewed, paid, overdue
  subtotal numeric,
  discount_label text,
  discount_amount numeric,
  total numeric,
  created_at timestamptz,
  sent_at timestamptz,
  paid_at timestamptz
)

-- Line Items
invoice_items (
  id uuid PK,
  invoice_id uuid FK → invoices,
  description text,
  detail text,               -- sub-description
  qty integer DEFAULT 1,
  rate numeric,
  amount numeric,
  sort_order integer
)
```

## FROM Info (Static)
- **Name:** Mike Slezak
- **Company:** Black Frog Built
- **Email:** mike@blackfrogbuilt.com
- **Phone:** (403) 305-7877
- **Website:** blackfrogbuilt.com

## Existing Invoice: AE Partners
- **Invoice #:** INV-2026-001
- **Client:** Ryan Alder, AE Partners
- **Address:** 11111 Katy Freeway, Suite 910, Houston, TX 77079
- **Project:** Compliance Management System (Full-Stack Web Application)
- **Subtotal:** $20,000.00
- **Discount:** Partnership Discount — -$8,000.00
- **Total Due:** $12,000.00
- **Terms:** Due on Receipt
- **Template file:** `collateral/invoice-aepartners.svg`

## File References
- Marketing site: `New-Project/index.html` (GitHub Pages)
- Invoice template engine: `collateral/invoice-template-locked.js`
- AE Partners invoice: `collateral/invoice-aepartners.svg`
- Frog logo (base64): `temp-image-href.txt`
- Brand guidelines: `blackfrog-branding/collateral/brand-guidelines.svg`
- Email signature: `blackfrog-branding/collateral/email-signature.svg`
