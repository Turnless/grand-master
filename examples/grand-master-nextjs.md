---
name: grand-master
description: The verified coding patterns for this project, covering how we fetch data, handle errors, name things, and where files go. Loaded every session through CLAUDE.md. Follow it before writing or changing any code.
---

# Grand-Master: How This Project Is Built

> **Example only.** This shows what a grand-master looks like after `/master-learn` and `/master-review` have run on a Next.js + TypeScript project. Yours will contain *your* patterns.
>
> **Status:** ACTIVE (6 rules)
> **Last reviewed:** 2026-10-06

(The "Rules for the AI" section is the same as in the template and is left out here.)

## 1. Project map: where things go (MAP)

### MAP-1: Each feature gets its own folder under `src/features/`
- **Do:** put a feature's components, hooks, API calls, and types together: `src/features/orders/`
- **Don't:** scatter one feature across global `components/`, `hooks/`, `api/` folders
- **Golden file:** `src/features/orders/`
- **Why:** everything for one feature lives in one place, so it's easy to find and delete.
- **Example:**
  ```
  src/features/orders/
  ├── OrderList.tsx        ← components
  ├── useOrders.ts         ← data hooks
  ├── orders.api.ts        ← raw API calls
  └── orders.types.ts      ← types
  ```

## 2. Data fetching (FETCH)

### FETCH-1: Components never fetch. They call a `useX` hook.
- **Do:** raw request in `<feature>.api.ts`, wrapped in a hook in `use<Thing>.ts`
- **Don't:** call `fetch()` or `axios` inside a component
- **Golden file:** `src/features/orders/useOrders.ts`
- **Why:** components stay simple, and the same data can be reused anywhere.
- **Example:**
  ```ts
  // orders.api.ts
  export async function getOrders(): Promise<Order[]> {
    const res = await fetch("/api/orders");
    if (!res.ok) throw new ApiError("Could not load orders", res.status);
    return res.json();
  }

  // useOrders.ts
  export function useOrders() {
    return useQuery({ queryKey: ["orders"], queryFn: getOrders });
  }
  ```

## 3. Error handling (ERR)

### ERR-1: API functions throw `ApiError`. The UI shows a toast.
- **Do:** throw `ApiError` (from `src/lib/errors.ts`) with a human-readable message, and catch it in the hook's `onError` with `toast.error(err.message)`
- **Don't:** swallow errors silently, or show raw error objects to users
- **Golden file:** `src/lib/errors.ts`
- **Why:** users always see a clear message, and every error looks the same.

## 4. Loading & empty states (LOAD)

### LOAD-1: Every list has a skeleton while loading and a message when empty
- **Do:** `if (isLoading) return <ListSkeleton />` and `if (!data.length) return <EmptyState text="No orders yet" />`
- **Golden file:** `src/features/orders/OrderList.tsx`

## 5. Naming (NAME)

### NAME-1: Components are PascalCase, everything else is camelCase
- **Do:** `OrderList.tsx`, `useOrders.ts`, `orders.api.ts`; booleans start with `is`/`has`; event handlers start with `handle`
- **Don't:** `order-list.tsx`, `loading`, `clickFn`

## 10. Never do this (NEVER)

### NEVER-1: No `any` type
- **Do:** write the real type, or use `unknown` and narrow it
- **Why:** `any` turns off TypeScript's safety checks.
