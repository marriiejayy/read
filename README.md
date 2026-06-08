# TECHNICAL DOCUMENTATION OF DIMP AND DIMPIFIED

**Repositories:** 
- `MigrationGFA/Dimpified` (Main Customer App)
- `MigrationGFA/Admin-DIMP-Dashboard` (Internal Admin Panel)


## Executive Summary

This report documents findings from initial onboarding, codebase exploration, and environment setup. The platform consists of two interconnected React applications serving completely different users:

**DIMPIFIED** which is a booking & business management platform used by Barbers, makeup artists, gym owners, customers and so on.

**DIMP (Admin)** | Internal administration dashboard | GFA employees (finance, support, admin) |

Both applications share the same backend/database but have different codebases, architectures, and feature sets.

## 2. Platform Overview

### DIMPIFIED

DIMPIFIED is a booking and business management platform for service professionals including:
- Barbers and hair stylists
- Makeup artists
- Nail technicians
- Spas and wellness centers
- Gyms and fitness trainers
- Dentists and dental clinics

On Dimpified, business Owners can: Create a professional booking website,  Set services and pricing, manage appointments, accept online payments, track analytics, send automated reminders

Customers can: Find local service providers, book appointments online, pay in advance and receive SMS/email reminders.

Visitors can: Browse marketing pages, compare pricing see templates before signing up

###  Key Features 
- Customizable website templates -30+ industry-specific designs 
- Free and paid subscription tiers -Multiple pricing plans 
- Country-specific pricing -20+ countries supported 
- Online payment processing -Stripe + Paystack integration 
- Automated SMS and email reminders -Customer notifications
- Business owner dashboard -Analytics and management
- Customer search pages -"Find barbers near me" 

### DIMP (Admin Dashboard)?

DIMP is the internal administration dashboard for company employees to manage the entire platform.The admins can
- View all users and see every business owner and customer
- Monitor subscriptions by tracking who is paying, and which plan 
- View transactions and see all payments across platform 
- Approve withdrawals and business owners request payouts 
- Manage support tickets, respond to customer issues 
- Create blog posts and write content for marketing site 
- Manage ISA agents and Oversee affiliate/referral program 
- View analytics and store locations by country/state 

###  Real-World Example Flow

```
1. Barber signs up on DIMPIFIED → creates account, sets services
2. Barber appears in DIMP under "Userbase" for admins to see
3. Customer books and pays on DIMPIFIED
4. Transaction appears in DIMP under "Transactions"
5. Barber requests withdrawal in DIMPIFIED
6. Admin approves it in DIMP under "Withdrawal"
```

---

## 3. Technology Stack

 **Frontend Framework**  React 
 **State Management**  Redux 
 **Styling** Tailwind CSS + Flowbite
 **Build Tool**  Vite 
 **Payments**  Stripe, Paystack 
 **Analytics** Google Analytics, Mixpanel, Meta Pixel
 **HTTP Client**  Axios with interceptors
 **PWA** Workbox (service worker) 

---

## 4. Codebase Architecture

### 4.1 Two-Application Structure

The platform consists of two separate React applications:
 DIMPIFIED (Main App)                      
  - Customer-facing business websites                        
  - 8 Redux slices                                           
  - 100+ routes                                              
  - 30+ templates                                            
 - Subdomain routing                                        

DIMP (Admin Dashboard)                   
- Internal staff management                                
- 1 Redux slice (auth only)                                
- 40+ routes                                               
- PWA installable                                          
- CSV export                                               



###  DIMPIFIED Architecture Flow

```
main.jsx (starts app, loads Redux, loads saved data)
        ↓
App.jsx (sets up analytics, gets user country)
        ↓
AllRoutes.jsx (maps URLs to pages, handles subdomains)
        ↓
Page Component (displays content)
        ↓
User interacts (clicks, types, submits)
        ↓
dispatch() sends action to Redux
        ↓
Reducer updates store
        ↓
Component re-renders with new data
```

### State Management (Redux)

The DIMPIFIED store has **8 slices (sections)** :

| `auth` | Login status, user info, token |
| `ecosystemDomain` | User's domain names |
| `activeSection` | Current page/section |
| `mainTemplate` | Active website template |
| `createNewService` | New service form data |
| `editTemplate` | Template changes |
| `ecosystemPlan` | Subscription plan |
| `ecosystemStatus` | System health |

**Data persists** — if you refresh the page, you stay logged in.

---

##  Main Application (DIMPIFIED)

### Routing Structure

| Route Pattern | What It Shows |
|---------------|---------------|
| `/` | Marketing homepage OR user's custom website (based on subdomain) |
| `/auth/*` | Login, signup, onboarding |
| `/creator/dashboard/*` | Business owner control panel |
| `/templates/*` | Template previews |
| `/{country}/pricing` | Country-specific pricing |

###  Subdomain Routing (Key Feature)

When someone visits `business-name.dimp.com`:

1. App extracts `business-name` (the subdomain)
2. Looks up which business owns it
3. Loads that business's template and data
4. Shows their custom website
- This is the core  of DIMPIFIED.

### 5.3 Folder Structure

The `src` folder in DIMPIFIED is organized by feature/function:

| Folder | Purpose |
|--------|---------|
| `src/pages/` | Full page components |
| `src/component/` | Reusable UI (buttons, inputs, modals) |
| `src/api/` | Backend API calls |
| `src/features/` | Redux slices (state management) |
| `src/helper/` | Utility functions |
| `src/hooks/` | Custom React hooks |
| `src/layout/` | Page shells (header, sidebar) |
| `src/assets/` | Images, icons, fonts |
| `src/dimp-templates/` | Website templates (30+ industries) |

### 5.4 AllRoutes.jsx (Navigation Control Center)

**`AllRoutes.jsx` is the navigation control center of the entire DIMPIFIED application.**

It contains over 100 route definitions that map every possible URL to its corresponding page component. The file uses lazy loading (`React.lazy()`) to load pages only when needed, which makes the app faster.

**Critical functions handled:**

| Function | Description |
|----------|-------------|
| **Subdomain detection** | `business-name.dimp.com` → business website |
| **Protected routes** | Dashboard requires login |
| **Country-specific pricing** | `/ng/pricing`, `/uk/pricing`, etc. |
| **Lazy loading** | Pages load only when visited |
| **Loading spinner** | Shows while pages load |

###  Landing Page 

The main marketing homepage was found at: src/pages/LandingPages/NewLandingPage.jsx
**This component assembles the page from multiple sections:**

| Section | Description |
|---------|-------------|
| Navbar | Navigation menu |
| Hero | Video banner with "Get Started" CTA |
| StatsBar | "Trusted by over 10,000+ businesses" |
| AboutSection | "Book smoothly, Earn more" |
| WhyChoose | 6 feature cards |
| GrowBusiness | Statistics (80% more clients, 95% fewer no-shows) |
| TestimonialsSection | Customer reviews with auto-rotating carousel |
| SubscriptionSection | Free and Lite pricing plans in NGN |
| Discover | "Get discovered by new customers" |
| Hero2 | Bottom purple CTA banner |
| FAQ | Accordion with 5 questions |
| Footer | Links and copyright |


### Public vs Private Pages

| Type | Examples | Account Required? |
|------|----------|-------------------|
| Marketing | `/barbers`, `/spa`, `/gym` | ❌ No |
| Customer search | `/barbers-near-me`, `/spa-near-me` | ❌ No |
| Business onboarding | `/ng/barbers/onboarding` | ✅ Yes (during signup) |
| Business dashboard | `/creator/dashboard/*` | ✅ Yes (login required) |

---

##  Admin Dashboard (DIMP)

### Overview

The Admin DIMP Dashboard is a separate, simpler application for internal staff use.

**Key differences from main DIMPIFIED:**

| Aspect | Main DIMPIFIED | Admin Dashboard |
|--------|----------------|-----------------|
| Redux slices | 8 slices | 1 slice (auth only) |
| Routes | 100+ | 40+ |
| Templates | 30+ templates | No templates |
| Subdomain routing | ✅ Yes | ❌ No |
| PWA installable | ❌ No | ✅ Yes |
| CSV export | ❌ No | ✅ Yes |
| Blog system | ❌ No | ✅ Yes |
| ISA/Affiliate | ❌ No | ✅ Yes |

### 6.2 DIMP Router Structure

All admin routes are under `/admin/`:

| Route Category | Examples | Purpose |
|----------------|----------|---------|
| Authentication | `/`, `/admin/forgot-password` | Admin login |
| User management | `/admin/userbase`, `/admin/userprofile/:id` | View/edit users |
| Financial | `/admin/transaction`, `/admin/withdrawal` | Monitor payments, approve payouts |
| Support | `/admin/support-ticket`, `/admin/viewticket/:id` | Customer support |
| Blog | `/admin/blog/manage`, `/admin/blog/create-post` | Content management |
| ISA affiliates | `/admin/isa/agents`, `/admin/isa/earnings` | Referral program management |
| Analytics | `/admin/storebycountry`, `/admin/storebystate` | Business intelligence |

### 6.3 PWA Features

The admin dashboard includes:
- Service worker for offline caching
- Install prompt button
- Works without internet (for cached pages)

**This makes it installable as a standalone app on mobile and desktop devices.**

---


### Areas for Improvement

 `main` branch | Make `development` default on GitHub 
Hardcoded staff names (admin) | Move to database 
Large route file | Split into feature-based modules 
.env file is exposed 
locked file configuration issue -yarn-lock and package-lock.json
- No .env.example 


### Summary

The DIMPIFIED platform is a **well-architected multi-tenant SaaS platform** with:

- **Two separate applications** serving different users (business owners vs internal staff)
- **Subdomain-based routing** enabling professional business websites
- **30+ industry templates** supporting diverse service businesses
- **Comprehensive business dashboard** for service management
- **Country-specific pricing** for 20+ countries
- **PWA support** for installable admin dashboard

### 11.2 Key Metrics

| Metric | DIMPIFIED (Main) | DIMP (Admin) |
|--------|------------------|--------------|
| Routes | 100+ | 40+ |
| Redux slices | 8 | 1 |
| Templates | 30+ | 0 |
| API modules | 20+ | 5 |
| Page folders | 30+ | 15+ |
| Countries supported | 20+ | N/A |



## Appendix A: Complete Route List (DIMPIFIED)

| Category | Routes |
|----------|--------|
| **Marketing** | `/`, `/about`, `/features`, `/barbers`, `/spa`, `/gym` |
| **Customer Search** | `/barbers-near-me`, `/spa-near-me`, `/find-barber` |
| **Authentication** | `/auth/login`, `/auth/register`, `/forgot-password` |
| **Onboarding** | `/auth/personal-info`, `/auth/business-info`, `/auth/verify-otp`, `/auth/select-template` |
| **Dashboard** | `/creator/dashboard/overview`, `/creator/dashboard/bookings`, `/creator/dashboard/payments`, `/creator/dashboard/edit-service`, `/creator/dashboard/edit-template` |
| **Templates** | `/templates/barber-modern`, `/templates/makeup-template`, `/templates/firstgym`, `/templates/firstspa` |
| **Pricing** | `/ng/pricing`, `/uk/pricing`, `/us/pricing`, `/gh/pricing` (20+ countries) |

---

## Appendix B: Permission Keys Reference (Admin Dashboard)

| Permission | Feature |
|------------|---------|
| `overView` | Dashboard homepage |
| `userBase` | User management |
| `subscription` | Subscription plans |
| `transaction` | Financial transactions |
| `withdrawal` | Withdrawal approval |
| `supportTicket` | Customer support |
| `subcategory` | Service categories |
| `store` | Store analytics |
| `isa` | ISA/affiliate management |
| `blog` | Blog management |
| `myrefferals` | Personal referrals |

---

## Appendix C: API Modules Summary (Admin Dashboard)

| Module | Purpose | Key Functions |
|--------|---------|---------------|
| `authRoute.js` | Authentication | `adminLogin`, `forgotPassword`, `resetPassword` |
| `dashboardApi.js` | Core data | `userInformation`, `withdrawalDetails`, `subscriptionData` |
| `isaApi.js` | ISA management | `getIsaAgents`, `getIsaEarnings`, `getIsaAgentProfile` |
| `blogApi.js` | Blog management | `createPost`, `updatePost`, `getAnalytics` |
| `affliate.js` | Affiliate program | `getReferrals`, `trackCommissions` |

---
