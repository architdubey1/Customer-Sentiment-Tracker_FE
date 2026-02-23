# Sentinel AI — Customer Early Warning System (Frontend)

A React-based dashboard for monitoring customer sentiment, analyzing email feedback, and prioritizing critical issues. Built with React 19, Vite, Tailwind CSS v4, and Recharts.

## Tech Stack

| Layer       | Technology                                       |
| ----------- | ------------------------------------------------ |
| Framework   | React 19                                         |
| Bundler     | Vite 7                                           |
| Styling     | Tailwind CSS v4 (via `@tailwindcss/vite` plugin) |
| Routing     | React Router v7                                  |
| HTTP Client | Axios                                            |
| Charts      | Recharts                                         |
| Icons       | Lucide React                                     |
| Utilities   | clsx (conditional classnames)                    |
| Linting     | ESLint 9 (flat config)                           |

## Project Structure

```
src/
├── components/
│   ├── Layout.jsx          # App shell — sidebar navigation + outlet
│   ├── PriorityBadge.jsx   # Color-coded priority label (critical/high/medium/low)
│   ├── SentimentBadge.jsx  # Color-coded sentiment label (positive/negative/neutral/mixed)
│   └── StatCard.jsx        # Reusable metric card with icon, value, and subtitle
├── lib/
│   └── api.js              # Centralized Axios API client
├── pages/
│   ├── Dashboard.jsx       # Overview stats, pie charts, trend chart, top complainers
│   ├── PriorityQueue.jsx   # Filterable list of feedback sorted by priority
│   ├── Feedback.jsx        # Paginated feedback table with sentiment/priority filters
│   ├── Customers.jsx       # Customer grid with sorting and pagination
│   └── CustomerDetail.jsx  # Single customer profile with feedback history
├── App.jsx                 # Route definitions
├── main.jsx                # Entry point — mounts React with BrowserRouter
└── index.css               # Tailwind CSS import
```

## Pages & Routes

| Route            | Page            | Description                                                     |
| ---------------- | --------------- | --------------------------------------------------------------- |
| `/`              | —               | Redirects to `/dashboard`                                       |
| `/dashboard`     | Dashboard       | High-level stats, sentiment/priority pie charts, 30-day trend   |
| `/priority`      | Priority Queue  | Expandable feedback items filtered by priority level            |
| `/feedback`      | Feedback        | Full feedback table with sentiment, priority, and sort filters  |
| `/customers`     | Customers       | Customer cards with avg sentiment score and feedback count      |
| `/customers/:id` | Customer Detail | Individual customer profile and their complete feedback history |

All routes are nested inside a `Layout` component that provides the sidebar navigation and a dark-themed app shell.

## Components

### Layout

The app shell with a fixed sidebar containing navigation links (Dashboard, Priority Queue, Feedback, Customers), the "Sentinel AI" logo, and a **Scan Emails** button that triggers the mail scan API endpoint. The active route is highlighted. Uses `<Outlet />` from React Router to render child pages.

### StatCard

Reusable card displaying a metric with an icon, label, value, and optional subtitle. Supports color variants: `indigo`, `red`, `emerald`, `amber`, `sky`.

### PriorityBadge / SentimentBadge

Small color-coded badges used throughout the app to visually distinguish priority levels and sentiment types. Both optionally display a numeric score.

## API Layer

All API calls are centralized in `src/lib/api.js` using Axios. The Vite dev server proxies `/api` requests to the backend at `http://localhost:5001`.

| Function               | Method | Endpoint                  | Purpose                               |
| ---------------------- | ------ | ------------------------- | ------------------------------------- |
| `fetchStats()`         | GET    | `/api/stats`              | Dashboard statistics                  |
| `fetchFeedback()`      | GET    | `/api/sentiment`          | List feedback (filterable, paginated) |
| `fetchPriorityQueue()` | GET    | `/api/sentiment/priority` | Priority-sorted feedback              |
| `fetchFeedbackById()`  | GET    | `/api/sentiment/:id`      | Single feedback item                  |
| `fetchCustomers()`     | GET    | `/api/customers`          | List customers (sortable, paginated)  |
| `fetchCustomerById()`  | GET    | `/api/customers/:id`      | Customer detail + feedback history    |
| `triggerMailScan()`    | POST   | `/api/mail/scan`          | Trigger email ingestion               |
| `analyzeSentiment()`   | POST   | `/api/sentiment/analyze`  | Analyze arbitrary text                |

## State Management

The app uses **local component state** (`useState`) — no global store (Redux, Zustand, etc.). Each page fetches its own data on mount or when filter/route params change via `useEffect`, and manages its own loading/error state.

**Data flow:**

```
User Action → State Update → useEffect → API Call → State Update → Re-render
```

## Routing

- `BrowserRouter` is initialized in `main.jsx`.
- Routes are defined in `App.jsx` using React Router v7's `<Routes>` and `<Route>`.
- All page routes are nested under the `Layout` route so the sidebar persists across navigation.
- `<Navigate>` handles the root redirect.
- `useNavigate` is used for programmatic navigation (e.g., clicking a customer card).
- `useParams` extracts route parameters (e.g., customer ID).

## Styling & Theme

- **Tailwind CSS v4** is integrated via the `@tailwindcss/vite` plugin — no separate `tailwind.config.js` needed.
- The app uses a **dark theme** (gray-950 backgrounds, gray-900 card surfaces).
- Color coding is used consistently:
  - **Priority:** red (critical), orange (high), amber (medium), emerald (low)
  - **Sentiment:** emerald (positive), red (negative), gray (neutral), amber (mixed)
- Layouts are responsive using Tailwind's grid utilities with breakpoints.
- `clsx` is used for conditional class composition.

## Getting Started

### Prerequisites

- Node.js 18+
- The backend API running on `http://localhost:5001`

### Install & Run

```bash
npm install
npm run dev
```

The dev server starts on **http://localhost:3001** and proxies all `/api` requests to the backend.

### Build for Production

```bash
npm run build
npm run preview   # preview the production build locally
```

### Lint

```bash
npm run lint
```

## Vite Configuration

- **React plugin** (`@vitejs/plugin-react`) for JSX transform and Fast Refresh.
- **Tailwind plugin** (`@tailwindcss/vite`) for CSS processing.
- Dev server runs on port **3001** with `strictPort: false` (falls back to next available port).
- API proxy: all requests to `/api` are forwarded to `http://localhost:5001`.
