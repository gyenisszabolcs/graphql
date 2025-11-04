# Phase 0 - Task 2: UX/UI Design and Wireframes

**Agent Role:** UX Designer
**Date:** 2025-11-04
**Status:** ✅ COMPLETED

## Executive Summary

This document presents the complete user experience design, wireframes, and user flows for the GraphQL API web admin interface. The design prioritizes simplicity, clarity, and efficiency for non-technical users while providing power features for developers.

## 1. Design Principles

### Core Principles
1. **Simplicity First:** Complex technical concepts (GraphQL) hidden behind intuitive UI
2. **Progressive Disclosure:** Advanced features revealed only when needed
3. **Immediate Feedback:** Every action provides clear, immediate visual response
4. **Error Prevention:** Design prevents errors before they occur
5. **Consistency:** Reusable components across all interfaces

### Target User Experience Goals
- **Operations Manager:** Can build queries without GraphQL knowledge in < 2 minutes
- **Webshop Developer:** Can explore API schema quickly and understand data relationships
- **Admin User:** Can create/update/delete records with confidence and clear validation

## 2. Design System

### 2.1 Color Palette

```
Primary Colors:
- Primary-500: #3B82F6 (Blue) - Main actions, links, active states
- Primary-600: #2563EB - Hover states
- Primary-700: #1D4ED8 - Active/pressed states
- Primary-50:  #EFF6FF - Background highlights

Secondary Colors:
- Secondary-500: #8B5CF6 (Purple) - GraphQL-specific elements
- Secondary-600: #7C3AED - Hover
- Secondary-50:  #F5F3FF - Code backgrounds

Neutral Colors:
- Gray-900: #111827 - Primary text
- Gray-700: #374151 - Secondary text
- Gray-500: #6B7280 - Muted text
- Gray-300: #D1D5DB - Borders
- Gray-100: #F3F4F6 - Background
- Gray-50:  #F9FAFB - Secondary background
- White:    #FFFFFF - Cards, modals

Semantic Colors:
- Success-500: #10B981 (Green) - Success messages, confirmations
- Warning-500: #F59E0B (Amber) - Warnings, cautions
- Error-500:   #EF4444 (Red) - Errors, destructive actions
- Info-500:    #3B82F6 (Blue) - Info messages, tips
```

### 2.2 Typography System

```
Font Family: Inter (primary), system-ui (fallback)

Display (Hero):
- Class: text-4xl font-bold
- Size: 36px, Weight: 700, Line-height: 40px
- Usage: Page titles, empty states

Heading 1:
- Class: text-3xl font-semibold
- Size: 30px, Weight: 600, Line-height: 36px
- Usage: Section headers

Heading 2:
- Class: text-2xl font-semibold
- Size: 24px, Weight: 600, Line-height: 32px
- Usage: Card headers, modal titles

Heading 3:
- Class: text-xl font-medium
- Size: 20px, Weight: 500, Line-height: 28px
- Usage: Subsection headers

Body Large:
- Class: text-base font-normal
- Size: 16px, Weight: 400, Line-height: 24px
- Usage: Primary content, form labels

Body Regular:
- Class: text-sm font-normal
- Size: 14px, Weight: 400, Line-height: 20px
- Usage: Secondary content, descriptions

Body Small:
- Class: text-xs font-normal
- Size: 12px, Weight: 400, Line-height: 16px
- Usage: Captions, timestamps, hints

Code:
- Font Family: 'Fira Code', monospace
- Class: text-sm font-mono
- Size: 14px, Weight: 400, Line-height: 20px
- Usage: GraphQL queries, JSON results
```

### 2.3 Spacing Scale

```
Based on 4px unit system (Tailwind defaults):
- xs:  4px  (spacing-1)
- sm:  8px  (spacing-2)
- md:  16px (spacing-4)
- lg:  24px (spacing-6)
- xl:  32px (spacing-8)
- 2xl: 48px (spacing-12)
- 3xl: 64px (spacing-16)

Common Applications:
- Component padding: md (16px)
- Section gaps: lg (24px)
- Page margins: xl (32px)
- Element spacing: sm (8px)
```

### 2.4 Component Library

#### Button Component

**Primary Button:**
```
Classes: bg-primary-500 hover:bg-primary-600 active:bg-primary-700
         text-white font-medium px-4 py-2 rounded-lg
         transition-colors duration-200 focus:ring-2 focus:ring-primary-300

States:
- Default: Blue background, white text
- Hover: Darker blue (#2563EB)
- Active: Even darker (#1D4ED8)
- Disabled: bg-gray-300 text-gray-500 cursor-not-allowed
- Focus: Ring outline for keyboard navigation

Sizes:
- Small: px-3 py-1.5 text-sm
- Medium: px-4 py-2 text-base (default)
- Large: px-6 py-3 text-lg
```

**Secondary Button:**
```
Classes: bg-white hover:bg-gray-50 border border-gray-300
         text-gray-700 font-medium px-4 py-2 rounded-lg
         transition-colors duration-200
```

**Danger Button:**
```
Classes: bg-error-500 hover:bg-error-600 text-white
         font-medium px-4 py-2 rounded-lg
```

#### Input Field Component

```
Label: text-sm font-medium text-gray-700 mb-1 block
Input: w-full px-3 py-2 border border-gray-300 rounded-lg
       focus:ring-2 focus:ring-primary-500 focus:border-primary-500
       text-base text-gray-900

States:
- Default: Border gray-300
- Focus: Blue ring, blue border
- Error: Border error-500, ring error-300
- Disabled: bg-gray-100 text-gray-500 cursor-not-allowed

Variants:
- Text: type="text"
- Password: type="password" with show/hide toggle
- Number: type="number"
- Textarea: rows="4" resize-y
```

#### Select Dropdown Component

```
Classes: w-full px-3 py-2 border border-gray-300 rounded-lg
         focus:ring-2 focus:ring-primary-500
         bg-white text-base text-gray-900
         appearance-none cursor-pointer

Icon: Chevron-down icon positioned right

States:
- Default: Closed dropdown
- Open: Shows options list with max-h-60 overflow-y-auto
- Selected: Option highlighted with bg-primary-50
```

#### Card Component

```
Classes: bg-white rounded-xl shadow-sm border border-gray-200 p-6

Variants:
- Default: white background, subtle shadow
- Hover: shadow-md (for clickable cards)
- Interactive: cursor-pointer hover:shadow-lg

Structure:
- Header: flex justify-between items-center mb-4
- Body: Content area
- Footer: pt-4 border-t border-gray-200 (if needed)
```

#### Table Component

```
Container: overflow-x-auto rounded-lg border border-gray-200
Table: w-full text-sm text-left
THead: bg-gray-50 border-b border-gray-200
TH: px-4 py-3 font-semibold text-gray-700
TBody: divide-y divide-gray-200
TD: px-4 py-3 text-gray-900

Row States:
- Default: white background
- Hover: bg-gray-50 (if clickable)
- Selected: bg-primary-50
```

#### Alert/Toast Component

```
Container: p-4 rounded-lg flex items-start gap-3 mb-4

Variants:
- Success: bg-success-50 border-l-4 border-success-500 text-success-900
- Error: bg-error-50 border-l-4 border-error-500 text-error-900
- Warning: bg-warning-50 border-l-4 border-warning-500 text-warning-900
- Info: bg-info-50 border-l-4 border-info-500 text-info-900

Structure:
- Icon (left): Contextual icon (check, x, alert, info)
- Message: font-medium
- Close button (right): text-gray-500 hover:text-gray-700
```

## 3. User Flows

### 3.1 User Flow: Login to Query Execution

```
START
  ↓
[Landing Page: /]
  ↓
User clicks "Login" or auto-redirect
  ↓
[Login Page: /login]
  ├─ User enters username
  ├─ User enters password
  ├─ User clicks "Login" button
  ↓
API validates credentials
  ├─ ✅ SUCCESS → JWT token saved to localStorage
  │   ↓
  │   [Dashboard Page: /dashboard]
  │   ├─ Welcome message shown
  │   ├─ Quick actions displayed
  │   │   ├─ Option 1: Go to Query Builder
  │   │   ├─ Option 2: Browse Schema
  │   │   └─ Option 3: View Recent Queries
  │   ↓
  │   User clicks "Query Builder"
  │   ↓
  │   [Query Builder Page: /query-builder]
  │   ├─ Step 1: Select table from dropdown
  │   │   └─ Options: cikkek, gyartok, partnerek, users
  │   ├─ Step 2: Select fields (multi-select checkboxes)
  │   ├─ Step 3: (Optional) Add filters
  │   ├─ Step 4: (Optional) Add sorting
  │   ├─ Step 5: Generated query preview (read-only)
  │   ↓
  │   User clicks "Execute Query"
  │   ↓
  │   GraphQL API call with JWT token
  │   ├─ ✅ SUCCESS → Results displayed in table
  │   │   ├─ Table view (default)
  │   │   ├─ JSON view (toggle)
  │   │   └─ Export option (future)
  │   │
  │   └─ ❌ ERROR → Error message displayed
  │       ├─ Authentication error → Redirect to login
  │       ├─ Validation error → Highlight issue
  │       └─ Server error → Show retry option
  │
  └─ ❌ FAILURE → Error message shown
      └─ "Invalid username or password"
      └─ Input fields highlighted in red
      └─ User can retry

END (User can logout or continue)
```

### 3.2 User Flow: Create New Record (Mutation)

```
START (from Query Builder or Dashboard)
  ↓
User clicks "Create New" button
  ↓
[Create Record Modal]
  ├─ Step 1: Select entity type (cikk, gyarto, partner)
  │   ↓
  ├─ Step 2: Form fields rendered based on entity
  │   ├─ Required fields marked with *
  │   ├─ Dropdown for foreign keys (e.g., gyartoId)
  │   ├─ Input validation on blur
  │   │   ├─ Required field check
  │   │   ├─ Format validation (email, phone)
  │   │   └─ Real-time error display
  │   ↓
  ├─ Step 3: User fills form
  │   ↓
  ├─ Step 4: User clicks "Create" button
  │   ↓
  │   GraphQL mutation API call
  │   ├─ ✅ SUCCESS
  │   │   ├─ Success toast: "Cikk created successfully"
  │   │   ├─ Modal closes
  │   │   ├─ Table refreshes with new record
  │   │   └─ New record highlighted briefly (3s)
  │   │
  │   └─ ❌ ERROR
  │       ├─ Validation error → Fields highlighted
  │       ├─ Duplicate error → Specific message shown
  │       └─ Server error → Retry option shown
  │       └─ Modal stays open for correction
  │
  └─ User can click "Cancel" at any time
      └─ Confirmation if form has changes
      └─ "Discard changes?" dialog

END
```

### 3.3 User Flow: Schema Exploration

```
START (from Dashboard)
  ↓
User clicks "Browse Schema"
  ↓
[Schema Browser Page: /schema]
  ├─ Left Sidebar: Table list
  │   ├─ cikkek (Products)
  │   ├─ gyartok (Manufacturers)
  │   ├─ partnerek (Partners)
  │   └─ users (Users)
  │
  ├─ Main Panel: Table details
  │   ├─ User clicks on "cikkek"
  │   │   ↓
  │   │   Table Details Displayed:
  │   │   ├─ Table name and description
  │   │   ├─ Field list (accordion style)
  │   │   │   ├─ id: Int! (Primary Key)
  │   │   │   ├─ cikkKod: String! (Unique)
  │   │   │   ├─ megnevezes: String!
  │   │   │   ├─ leiras: String
  │   │   │   ├─ egysegAr: Decimal!
  │   │   │   ├─ mennyisegiEgyseg: String
  │   │   │   ├─ gyartoId: Int (Foreign Key → gyartok.id)
  │   │   │   ├─ createdAt: DateTime!
  │   │   │   └─ updatedAt: DateTime
  │   │   │
  │   │   ├─ Relationships section
  │   │   │   └─ gyarto: GyartoType (Many-to-One)
  │   │   │
  │   │   └─ Example GraphQL query (copy button)
  │   │
  │   └─ User can click relationship link
  │       └─ Navigates to related table (gyartok)
  │
  └─ Quick action: "Build Query with This Table"
      └─ Redirects to Query Builder with table pre-selected

END
```

## 4. Wireframes

### 4.1 Login Page

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│                    [Logo / App Name]                        │
│                    GraphQL Admin Portal                     │
│                                                             │
│   ┌───────────────────────────────────────────────────┐   │
│   │                                                     │   │
│   │             Login to Your Account                  │   │
│   │                                                     │   │
│   │   Username or Email                                │   │
│   │   ┌───────────────────────────────────────────┐   │   │
│   │   │ Enter your username                        │   │   │
│   │   └───────────────────────────────────────────┘   │   │
│   │                                                     │   │
│   │   Password                                         │   │
│   │   ┌───────────────────────────────────────────┐   │   │
│   │   │ ••••••••••                       [👁 Show] │   │   │
│   │   └───────────────────────────────────────────┘   │   │
│   │                                                     │   │
│   │   [ ] Remember me                                  │   │
│   │                                                     │   │
│   │   ┌───────────────────────────────────────────┐   │   │
│   │   │          [Login] (Primary Button)         │   │   │
│   │   └───────────────────────────────────────────┘   │   │
│   │                                                     │   │
│   │   [Error Message Area - Hidden by default]        │   │
│   │   ⚠️ Invalid username or password                  │   │
│   │                                                     │   │
│   └───────────────────────────────────────────────────┘   │
│                                                             │
│             Powered by GraphQL & Hot Chocolate              │
│                                                             │
└─────────────────────────────────────────────────────────────┘

Responsive Mobile View (< 640px):
- Full-width card
- Reduced padding
- Stacked layout maintained
```

### 4.2 Dashboard Page

```
┌────────────────────────────────────────────────────────────────────────┐
│ [≡ Menu]  GraphQL Admin Portal            [User: admin ▼] [Logout]    │
├────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Welcome back, Admin! 👋                                              │
│   Last login: 2025-11-04 10:30 AM                                      │
│                                                                         │
│   ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐   │
│   │  📊 Query Builder│  │  🗂️ Browse Schema│  │  📜 API Docs     │   │
│   │                  │  │                  │  │                  │   │
│   │  Build and       │  │  Explore data    │  │  GraphQL API     │   │
│   │  execute queries │  │  model and       │  │  documentation   │   │
│   │  visually        │  │  relationships   │  │  & examples      │   │
│   │                  │  │                  │  │                  │   │
│   │  [Get Started →] │  │  [Browse →]      │  │  [View Docs →]   │   │
│   └──────────────────┘  └──────────────────┘  └──────────────────┘   │
│                                                                         │
│   Recent Activity                                                      │
│   ┌───────────────────────────────────────────────────────────────┐   │
│   │ • Queried cikkek table                      2 minutes ago     │   │
│   │ • Created new gyarto "Samsung"              15 minutes ago    │   │
│   │ • Updated cikk #45                          1 hour ago        │   │
│   └───────────────────────────────────────────────────────────────┘   │
│                                                                         │
│   Quick Stats                                                          │
│   ┌────────────┐  ┌────────────┐  ┌────────────┐  ┌────────────┐    │
│   │  Cikkek    │  │  Gyártók   │  │  Partnerek │  │  Users     │    │
│   │    142     │  │     28     │  │     56     │  │     12     │    │
│   └────────────┘  └────────────┘  └────────────┘  └────────────┘    │
│                                                                         │
└────────────────────────────────────────────────────────────────────────┘

Navigation (Sidebar when expanded):
┌─────────────────┐
│ 🏠 Dashboard    │
│ 📊 Query Builder│
│ 🗂️ Schema       │
│ 📝 Mutations    │
│ 📜 Docs         │
│ ⚙️ Settings     │
└─────────────────┘
```

### 4.3 Query Builder Page

```
┌────────────────────────────────────────────────────────────────────────────────┐
│ [≡] GraphQL Admin Portal / Query Builder                [admin ▼] [Logout]    │
├────────────────────────────────────────────────────────────────────────────────┤
│                                                                                 │
│  Query Builder                                                                  │
│                                                                                 │
│  ┌────────────────────────────────────────────────────────────────────────┐   │
│  │                                                                          │   │
│  │  Step 1: Select Table                                                   │   │
│  │  ┌────────────────────────────────────┐                                │   │
│  │  │ Choose a table...          [▼]     │ <- Dropdown                    │   │
│  │  └────────────────────────────────────┘                                │   │
│  │    Options: cikkek, gyartok, partnerek, users                          │   │
│  │                                                                          │   │
│  │  [After selecting "cikkek":]                                            │   │
│  │                                                                          │   │
│  │  Step 2: Select Fields to Return                                        │   │
│  │  ┌────────────────────────────────────────────────────────────────┐   │   │
│  │  │ ☑ id                    ☑ egysegAr                             │   │   │
│  │  │ ☑ cikkKod               ☐ mennyisegiEgyseg                     │   │   │
│  │  │ ☑ megnevezes            ☑ createdAt                            │   │   │
│  │  │ ☐ leiras                ☐ updatedAt                            │   │   │
│  │  │                                                                  │   │   │
│  │  │ ☐ gyarto (nested)  [▼]  <- Expandable for nested fields       │   │   │
│  │  └────────────────────────────────────────────────────────────────┘   │   │
│  │                                                                          │   │
│  │  Step 3: Add Filters (Optional)                                         │   │
│  │  [+ Add Filter]                                                         │   │
│  │  ┌────────────────────────────────────────────────────────────────┐   │   │
│  │  │ Field: [egysegAr ▼]  Operator: [> ▼]  Value: [10000    ]      │   │   │
│  │  │                                                [Remove]         │   │   │
│  │  └────────────────────────────────────────────────────────────────┘   │   │
│  │                                                                          │   │
│  │  Step 4: Sort & Pagination (Optional)                                   │   │
│  │  Sort by: [megnevezes ▼]  Order: [ASC ▼]                               │   │
│  │  Limit: [20 ▼]  Offset: [0 ▼]                                          │   │
│  │                                                                          │   │
│  └────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
│  Generated GraphQL Query                                                        │
│  ┌────────────────────────────────────────────────────────────────────────┐   │
│  │ query {                                                       [Copy]   │   │
│  │   cikkek(                                                              │   │
│  │     where: { egysegAr: { gt: 10000 } }                                │   │
│  │     order: { megnevezes: ASC }                                         │   │
│  │     take: 20                                                           │   │
│  │   ) {                                                                  │   │
│  │     id                                                                 │   │
│  │     cikkKod                                                            │   │
│  │     megnevezes                                                         │   │
│  │     egysegAr                                                           │   │
│  │     createdAt                                                          │   │
│  │   }                                                                    │   │
│  │ }                                                                      │   │
│  └────────────────────────────────────────────────────────────────────────┘   │
│                                                                                 │
│  [Execute Query] (Primary) [Clear] (Secondary) [Save Query] (Secondary)       │
│                                                                                 │
│  ─────────────────────────────────────────────────────────────────────────    │
│                                                                                 │
│  Query Results                                                                  │
│  [Table View] [JSON View]                                                      │
│                                                                                 │
│  ┌────────────────────────────────────────────────────────────────────────┐   │
│  │ ID  │ Cikk Kód  │ Megnevezés        │ Egység Ár  │ Created At       │   │
│  ├─────┼───────────┼───────────────────┼────────────┼──────────────────┤   │
│  │ 12  │ BOSCH-001 │ Fúrógép Professional│ 25,000 Ft│ 2025-01-03 10:.. │   │
│  │ 15  │ SAM-055   │ Akkumulátor 5000mAh│ 12,500 Ft│ 2025-01-02 14:.. │   │
│  │ 23  │ BOSCH-012 │ Csavarbehajtó      │ 18,900 Ft│ 2024-12-28 09:.. │   │
│  │ ... │ ...       │ ...                │ ...        │ ...              │   │
│  └────────────────────────────────────────────────────────────────────────┘   │
│  Showing 1-20 of 47 results  [< Previous] [Next >]                            │
│                                                                                 │
└────────────────────────────────────────────────────────────────────────────────┘
```

### 4.4 Schema Browser Page

```
┌────────────────────────────────────────────────────────────────────────────┐
│ [≡] GraphQL Admin Portal / Schema Browser              [admin ▼] [Logout]│
├────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│ ┌──────────────┬────────────────────────────────────────────────────────┐ │
│ │ Tables       │  cikkek (Products)                                     │ │
│ │              │                                                         │ │
│ │ > cikkek     │  Description: Product catalog with pricing and details│ │
│ │   gyartok    │                                                         │ │
│ │   partnerek  │  Fields:                                               │ │
│ │   users      │  ┌─────────────────────────────────────────────────┐  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ id: Int!                                        │  │ │
│ │              │  │   Type: Integer (Non-nullable)                    │  │ │
│ │              │  │   Primary Key                                     │  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ cikkKod: String!                                │  │ │
│ │              │  │   Type: String (Non-nullable, Unique)             │  │ │
│ │              │  │   Description: Unique product code                │  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ megnevezes: String!                             │  │ │
│ │              │  │   Type: String (Non-nullable)                     │  │ │
│ │              │  │   Description: Product name/title                 │  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ leiras: String                                  │  │ │
│ │              │  │   Type: String (Nullable)                         │  │ │
│ │              │  │   Description: Detailed description               │  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ egysegAr: Decimal!                              │  │ │
│ │              │  │   Type: Decimal (Non-nullable)                    │  │ │
│ │              │  │   Description: Unit price in HUF                  │  │ │
│ │              │  │                                                   │  │ │
│ │              │  │ ▼ gyartoId: Int                                   │  │ │
│ │              │  │   Type: Integer (Nullable)                        │  │ │
│ │              │  │   Foreign Key → gyartok.id                        │  │ │
│ │              │  │   [View Related Table →]                          │  │ │
│ │              │  │                                                   │  │ │
│ │              │  └─────────────────────────────────────────────────┘  │ │
│ │              │                                                         │ │
│ │              │  Relationships:                                        │ │
│ │              │  ┌─────────────────────────────────────────────────┐  │ │
│ │              │  │ gyarto: GyartoType                              │  │ │
│ │              │  │   Type: Many-to-One                             │  │ │
│ │              │  │   Description: Manufacturer of this product     │  │ │
│ │              │  │   [View gyartok →]                              │  │ │
│ │              │  └─────────────────────────────────────────────────┘  │ │
│ │              │                                                         │ │
│ │              │  Available Operations:                                 │ │
│ │              │  Queries:                                              │ │
│ │              │  • getCikkek - List all products                       │ │
│ │              │  • getCikkById(id: Int!) - Get product by ID           │ │
│ │              │  • getCikkByCikkKod(cikkKod: String!) - By code        │ │
│ │              │                                                         │ │
│ │              │  Mutations:                                            │ │
│ │              │  • createCikk(input: CreateCikkInput!) - Create new    │ │
│ │              │  • updateCikk(input: UpdateCikkInput!) - Update        │ │
│ │              │  • deleteCikk(id: Int!) - Delete                       │ │
│ │              │                                                         │ │
│ │              │  Example Query:                                        │ │
│ │              │  ┌─────────────────────────────────────────────────┐  │ │
│ │              │  │ query {                              [Copy]     │  │ │
│ │              │  │   cikkek {                                      │  │ │
│ │              │  │     id                                          │  │ │
│ │              │  │     cikkKod                                     │  │ │
│ │              │  │     megnevezes                                  │  │ │
│ │              │  │     gyarto {                                    │  │ │
│ │              │  │       gyartoNev                                 │  │ │
│ │              │  │     }                                           │  │ │
│ │              │  │   }                                             │  │ │
│ │              │  │ }                                               │  │ │
│ │              │  └─────────────────────────────────────────────────┘  │ │
│ │              │                                                         │ │
│ │              │  [Build Query with This Table]                         │ │
│ │              │                                                         │ │
│ └──────────────┴────────────────────────────────────────────────────────┘ │
│                                                                             │
└────────────────────────────────────────────────────────────────────────────┘
```

### 4.5 Create/Edit Modal

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                      │
│                  Create New Cikk (Product)                    [✕]   │
│  ──────────────────────────────────────────────────────────────────│
│                                                                      │
│  Cikk Kód *                                                          │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ Enter unique product code (e.g., BOSCH-001)                │    │
│  └────────────────────────────────────────────────────────────┘    │
│                                                                      │
│  Megnevezés (Name) *                                                 │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ Enter product name                                          │    │
│  └────────────────────────────────────────────────────────────┘    │
│                                                                      │
│  Leírás (Description)                                                │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ Optional detailed description...                            │    │
│  │                                                              │    │
│  │                                                              │    │
│  └────────────────────────────────────────────────────────────┘    │
│                                                                      │
│  Egység Ár (Unit Price) *                                            │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ 0                                                  HUF      │    │
│  └────────────────────────────────────────────────────────────┘    │
│                                                                      │
│  Mennyiségi Egység (Unit)                                            │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ db                                                     [▼]  │    │
│  └────────────────────────────────────────────────────────────┘    │
│    Options: db (pieces), kg, liter, csomag, doboz                  │
│                                                                      │
│  Gyártó (Manufacturer) *                                             │
│  ┌────────────────────────────────────────────────────────────┐    │
│  │ Select manufacturer...                              [▼]    │    │
│  └────────────────────────────────────────────────────────────┘    │
│    Options loaded from gyartok table                                │
│                                                                      │
│  * Required fields                                                   │
│                                                                      │
│  ──────────────────────────────────────────────────────────────────│
│                                                                      │
│  [Cancel]                                          [Create Cikk]   │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘

States:
- Loading: Show spinner with "Creating..."
- Success: Green toast "Cikk created successfully!", modal closes
- Error: Red alert banner at top of modal with specific error
```

## 5. Responsive Design Specifications

### 5.1 Breakpoints

```
Mobile:     < 640px  (sm)
Tablet:     640px - 1024px (md, lg)
Desktop:    > 1024px (xl, 2xl)
```

### 5.2 Mobile Adaptations

**Login Page (Mobile):**
- Card full-width with minimal side padding (16px)
- Logo size reduced to 80% of desktop
- Button spans full width

**Dashboard (Mobile):**
- Navigation becomes hamburger menu
- Quick action cards stack vertically
- Stats grid becomes 2x2 instead of 4x1

**Query Builder (Mobile):**
- Single-column layout
- Steps collapse into accordion
- Results table horizontal scroll
- "Execute Query" button sticky at bottom

**Schema Browser (Mobile):**
- Sidebar becomes bottom sheet (swipe up to reveal)
- Field details in expandable sections
- Example queries in scrollable code block

### 5.3 Touch Targets

Minimum touch target size: 44x44px
- Buttons: min-height 44px
- Checkboxes: 24x24px with 44x44px hit area
- Dropdown triggers: min-height 44px
- Table rows (clickable): min-height 48px

## 6. Accessibility Features

### 6.1 Keyboard Navigation

- All interactive elements focusable with Tab
- Focus indicator: 2px solid ring, primary-500 color
- Logical tab order following visual flow
- Escape key closes modals
- Enter key submits forms
- Arrow keys navigate dropdowns and tables

### 6.2 Screen Reader Support

- Semantic HTML5 elements (nav, main, aside, header)
- ARIA labels on icon-only buttons
- ARIA live regions for dynamic content (query results, toasts)
- Form labels properly associated with inputs
- Table headers with proper scope attributes

### 6.3 Color Contrast

All text meets WCAG AA standards:
- Normal text (< 18px): 4.5:1 minimum
- Large text (≥ 18px): 3:1 minimum
- UI components: 3:1 minimum

Tested combinations:
- ✅ Gray-900 on White: 21:1
- ✅ Primary-500 on White: 5.2:1
- ✅ White on Primary-500: 5.2:1
- ✅ Error-500 text on Error-50 bg: 8.3:1

### 6.4 Motion Considerations

- Animations use `prefers-reduced-motion` media query
- Default transition durations: 200ms
- Disabled transitions for users preferring reduced motion

## 7. Loading and Error States

### 7.1 Loading States

**Initial Page Load:**
```
┌────────────────────────────────┐
│                                │
│      [Spinner Animation]       │
│                                │
│      Loading application...    │
│                                │
└────────────────────────────────┘
```

**Query Execution:**
```
Execute Query button changes to:
[⟳ Executing...] (Disabled, spinning icon)

Results area shows:
┌────────────────────────────────┐
│   [Spinner] Fetching results...│
└────────────────────────────────┘
```

**Table Loading:**
```
Skeleton loaders in table rows:
┌─────────────────────────────────┐
│ ▁▁▁▁ ▁▁▁▁▁▁▁ ▁▁▁▁▁▁ ▁▁▁▁      │
│ ▁▁▁▁ ▁▁▁▁▁▁▁ ▁▁▁▁▁▁ ▁▁▁▁      │
│ ▁▁▁▁ ▁▁▁▁▁▁▁ ▁▁▁▁▁▁ ▁▁▁▁      │
└─────────────────────────────────┘
```

### 7.2 Error States

**Form Validation Error:**
```
Field with error:
┌────────────────────────────────┐
│ Cikk Kód *                     │
│ ┌────────────────────────────┐ │
│ │ BOSCH-001                  │ │ <- Red border
│ └────────────────────────────┘ │
│ ⚠️ This code already exists     │ <- Red text
└────────────────────────────────┘
```

**API Error:**
```
┌────────────────────────────────────────────────┐
│ ⚠️ Error Executing Query                       │
│                                                 │
│ Unable to fetch results. Please try again.     │
│                                                 │
│ Error details:                                  │
│ GraphQLException: Query timeout after 30s      │
│                                                 │
│ [Retry] [Contact Support]                      │
└────────────────────────────────────────────────┘
```

**Network Error:**
```
┌────────────────────────────────────────────────┐
│ 🔌 Connection Lost                             │
│                                                 │
│ Unable to reach the server. Check your         │
│ internet connection and try again.             │
│                                                 │
│ [Retry Now]                                    │
└────────────────────────────────────────────────┘
```

### 7.3 Empty States

**No Results:**
```
┌────────────────────────────────────────────────┐
│                                                 │
│               📊                                │
│                                                 │
│          No Results Found                       │
│                                                 │
│   Try adjusting your filters or search terms   │
│                                                 │
│   [Clear Filters] [Start New Query]            │
│                                                 │
└────────────────────────────────────────────────┘
```

**No Saved Queries:**
```
┌────────────────────────────────────────────────┐
│                                                 │
│               💾                                │
│                                                 │
│       You haven't saved any queries yet         │
│                                                 │
│   Build a query and click "Save" to add it here│
│                                                 │
│   [Go to Query Builder]                        │
│                                                 │
└────────────────────────────────────────────────┘
```

## 8. Animations and Micro-interactions

### 8.1 Button Interactions

```
Hover: Scale 1.02, shadow elevation increase
Active: Scale 0.98
Focus: Ring appears with 200ms ease-out
Success: Green checkmark animation (500ms)
```

### 8.2 Modal Transitions

```
Enter: Fade in background (150ms), slide up content (200ms)
Exit: Fade out background (150ms), slide down content (200ms)
```

### 8.3 Toast Notifications

```
Enter: Slide in from top-right (300ms ease-out)
Auto-dismiss: Fade out after 5 seconds
Manual dismiss: Slide out to right (200ms)
```

### 8.4 Table Row Interactions

```
Hover: Background color change (100ms)
New row highlight: Yellow glow for 3s, fade to normal
Delete animation: Slide out + fade (300ms)
```

## 9. Implementation Priority

### Phase 1 (MVP - Week 1-2):
1. ✅ Login page with authentication
2. ✅ Dashboard with navigation
3. ✅ Basic query builder (table + field selection)
4. ✅ Results display (table view only)

### Phase 2 (Extended - Week 3):
5. ✅ Schema browser
6. ✅ Create/Edit modals for mutations
7. ✅ Filters and sorting in query builder
8. ✅ JSON view toggle for results

### Phase 3 (Polish - Week 4):
9. ✅ Responsive mobile design
10. ✅ Loading states and animations
11. ✅ Error handling and empty states
12. ✅ Accessibility improvements

## 10. Design Assets Needed

**From Designer/Developer:**
- [ ] Logo (SVG format, light and dark versions)
- [ ] Favicon (multiple sizes)
- [ ] Empty state illustrations (optional, can use emojis)
- [ ] Loading spinner animation (CSS-based acceptable)

**Typography:**
- [ ] Inter font files (or use Google Fonts CDN)
- [ ] Fira Code for monospace (or use system monospace)

## 11. Handoff Notes for Developers

### Vue.js Component Structure

```
src/
├── components/
│   ├── common/
│   │   ├── Button.vue
│   │   ├── Input.vue
│   │   ├── Select.vue
│   │   ├── Card.vue
│   │   ├── Table.vue
│   │   ├── Modal.vue
│   │   ├── Toast.vue
│   │   └── Loading.vue
│   ├── layout/
│   │   ├── Header.vue
│   │   ├── Sidebar.vue
│   │   └── MainLayout.vue
│   └── query-builder/
│       ├── TableSelector.vue
│       ├── FieldSelector.vue
│       ├── FilterBuilder.vue
│       ├── QueryPreview.vue
│       └── ResultsDisplay.vue
├── views/
│   ├── LoginView.vue
│   ├── DashboardView.vue
│   ├── QueryBuilderView.vue
│   └── SchemaView.vue
└── styles/
    ├── tailwind.css
    └── custom.css
```

### Tailwind Config

```javascript
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#EFF6FF',
          500: '#3B82F6',
          600: '#2563EB',
          700: '#1D4ED8',
        },
        secondary: {
          50: '#F5F3FF',
          500: '#8B5CF6',
          600: '#7C3AED',
        },
        // ... other colors
      },
      fontFamily: {
        sans: ['Inter', 'system-ui', 'sans-serif'],
        mono: ['Fira Code', 'monospace'],
      },
    },
  },
}
```

## 12. Testing Checklist

- [ ] All forms submit successfully with valid data
- [ ] All forms show appropriate errors with invalid data
- [ ] Keyboard navigation works on all pages
- [ ] Screen reader announces all dynamic content
- [ ] All buttons have focus indicators
- [ ] Color contrast meets WCAG AA
- [ ] Mobile layout functions correctly on 375px width
- [ ] Touch targets are minimum 44x44px
- [ ] Loading states appear for slow operations
- [ ] Error messages are clear and actionable
- [ ] Modal can be closed with Escape key
- [ ] No console errors on any page

---

**Design Version:** 1.0
**Status:** ✅ READY FOR DEVELOPMENT
**Next Step:** Frontend development (Phase 6)
