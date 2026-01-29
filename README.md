# Domus Living

A curated, premium platform for professionally managed co-living and long-stay sanctuaries.

**Live Demo:** https://abstraxlab.com/domus

---

## Business Overview

### Vision

Domus Living serves remote professionals and relocating individuals who want **clarity, calm, and quality** — not endless listings.

> Domus does not aim to be the biggest marketplace. It aims to be the **most trusted**.

### The Problem

**For Consumers:**
- Property portals are noisy and uncurated
- Co-living quality varies wildly
- Pricing is unclear (utilities, deposits, minimum stay)
- No trust signal for work-ready living
- High-quality locations are hard to discover

**For Operators:**
- High acquisition costs
- Low-quality enquiries
- No premium distribution channel
- Competing next to low-end supply damages brand

### The Solution

Domus Living acts like a **luxury letting bureau**:
- Manual operator approval
- Limited supply per city
- Clear standards (pricing, Wi-Fi, workspace)
- Calm, editorial presentation
- Expansion by geography, not volume

### Target Market

**Users:**
- Remote professionals (25–45)
- Digital nomads
- Relocating workers
- Professional / postgraduate students

**Paying Customers:**
- Professional co-living operators
- Hospitality-led long-stay providers
- Retreat / sanctuary operators

### Product Pillars

| Pillar | Description |
|--------|-------------|
| **Urban Co-Living** | Major cities, professionally managed buildings, private rooms/studios, work-ready living |
| **Domus Sanctuaries** | Quiet nature-based locations, long-stay hospitality-led, higher standards, premium positioning |

### Business Model

| Phase | Revenue Streams |
|-------|-----------------|
| **Launch** | Free listings for selected operators, featured placements, light pay-per-lead |
| **Scale** | Monthly operator subscriptions (€300–€550), visibility boosts, city collections |
| **Maturity** | Sanctuary premium packages, data & demand insights, institutional subscriptions |

*Tenants never pay to browse.*

---

## Technical Architecture

### Tech Stack

| Layer | Technology |
|-------|------------|
| **Framework** | Next.js 14 (App Router) |
| **Language** | TypeScript |
| **Styling** | Tailwind CSS |
| **Data** | JSON file with Repository Pattern |
| **Deployment** | GitHub Pages (Static Export) |

### Why These Choices?

**Next.js 14 with App Router**
- Server-side rendering capabilities for SEO (critical for city pages)
- Static export for zero-cost hosting on GitHub Pages
- Modern React patterns with Server Components
- Built-in routing with dynamic segments

**Repository Pattern**
- Abstracts data access behind interfaces
- Easy swap from JSON to real database (AWS, Supabase, etc.)
- Clean separation of concerns
- Testable architecture

**Tailwind CSS**
- Rapid UI development
- Consistent design system
- Small bundle size with purging
- Perfect for editorial, minimal design

**Static Export + GitHub Pages**
- Zero hosting costs
- Fast global CDN
- Simple deployment pipeline
- No server maintenance

### Project Structure

```
domus/
├── src/
│   ├── app/                      # Next.js App Router pages
│   │   ├── page.tsx              # Homepage
│   │   ├── cities/[slug]/        # Dynamic city pages
│   │   ├── listings/[id]/        # Individual listing details
│   │   ├── properties/[id]/      # Co-living property pages
│   │   ├── sanctuaries/          # Sanctuary collection
│   │   ├── operators/            # For operators page
│   │   └── waitlist/             # Waitlist/apply page
│   │
│   ├── components/
│   │   ├── ui/                   # Base UI (Button, Badge, Card, ImageGallery)
│   │   ├── layout/               # Header, Footer, Container
│   │   ├── listings/             # ListingCard, ListingGrid, PropertyCard
│   │   └── home/                 # Hero, CitySection, TrustBadges
│   │
│   ├── data/
│   │   └── db.json               # JSON "database"
│   │
│   ├── repositories/
│   │   ├── interfaces/           # Repository contracts
│   │   ├── json/                 # JSON implementations
│   │   └── index.ts              # Repository exports (swap point)
│   │
│   ├── types/
│   │   └── index.ts              # TypeScript interfaces
│   │
│   └── lib/
│       └── utils.ts              # Utility functions
│
├── public/                       # Static assets
├── out/                          # Static export (generated)
└── next.config.js                # Next.js config with basePath
```

### Repository Pattern Implementation

**Interfaces (contracts):**

```typescript
// repositories/interfaces/ListingRepository.ts
interface ListingRepository {
  getAll(): Promise<Listing[]>
  getById(id: string): Promise<Listing | null>
  getByCity(cityId: string): Promise<Listing[]>
  getByPropertyName(propertyName: string): Promise<Listing[]>
  getSanctuaries(): Promise<Listing[]>
  getFiltered(filters: ListingFilters): Promise<Listing[]>
  getFeatured(limit?: number): Promise<Listing[]>
  getStandalone(): Promise<Listing[]>
}
```

**To swap data source:**
1. Create new implementation (e.g., `AwsListingRepository`)
2. Change export in `repositories/index.ts`
3. No other code changes needed

### Data Model

```typescript
// Core entities
interface City {
  id: string
  name: string
  slug: string
  country: string
  description: string
  image: string
}

interface Property {
  id: string
  name: string
  cityId: string
  operatorId: string
  description: string
  neighborhood: string
  image: string
  images: string[]
  amenities: string[]
  totalRooms: number
}

interface Listing {
  id: string
  title: string
  cityId: string
  operatorId: string
  propertyName?: string      // Links room to property (co-living)
  type: 'studio' | 'private-room' | 'apartment'
  pricePerMonth: number
  minimumStay: number
  availableFrom: string
  images: string[]
  amenities: string[]
  badges: Badge[]
  isSanctuary: boolean
  // ...
}

interface Operator {
  id: string
  name: string
  verified: boolean
}
```

### Key Architectural Decisions

| Decision | Rationale |
|----------|-----------|
| **Property → Rooms hierarchy** | Co-living houses show as single card with "X rooms available", linking to individual rooms |
| **Standalone listings** | Studios/apartments without `propertyName` display independently |
| **Badge system** | "Work-Ready", "Verified Pricing", "Co-Living House" as trust signals |
| **All-inclusive pricing** | Every listing shows total monthly cost (utilities, WiFi, cleaning included) |
| **Static generation** | All pages pre-rendered at build time for performance and SEO |
| **Image carousel on mobile** | Desktop shows grid, mobile shows swipeable carousel |

---

## Design System

### Design Principles (from Business Plan)

- Neutral palette
- Generous spacing
- Few listings per page
- Editorial typography
- No dark patterns

> User feeling: "This feels considered."

### Color Palette

```css
--color-background: #FAFAF9    /* Warm white */
--color-surface: #FFFFFF
--color-text: #1C1917           /* Warm black */
--color-text-muted: #78716C
--color-accent: #292524         /* Dark brown/black */
--color-border: #E7E5E4
```

### Typography

- **Headlines:** Serif font (editorial feel)
- **Body:** Clean sans-serif
- **Generous line-height and letter-spacing**

### Component Variants

**Badges:**
- `default` - Neutral (amenities)
- `success` - Green (private bathroom)
- `accent` - Brand color (rooms available)
- `secondary` - Amber (Co-Living House indicator)

---

## Pages Implemented

| Page | Route | Purpose |
|------|-------|---------|
| Homepage | `/` | Hero with search, city grid, featured spaces, trust badges |
| City Page | `/cities/[slug]` | City intro, curated listings, Domus standards |
| Property Page | `/properties/[id]` | Co-living house details, available rooms |
| Listing Page | `/listings/[id]` | Individual room/studio details, pricing, enquiry CTA |
| Sanctuaries | `/sanctuaries` | Premium nature-based retreats collection |
| For Operators | `/operators` | Benefits, standards, application CTA |
| Waitlist | `/waitlist` | Demand capture, city requests |

---

## Deployment

### GitHub Pages Configuration

```javascript
// next.config.js
const nextConfig = {
  output: 'export',           // Static HTML export
  basePath: '/domus',         // Subdirectory deployment
  images: { unoptimized: true }
}
```

### Deploy Commands

```bash
# Build static export
npm run build

# Deploy to GitHub Pages
cd out
touch .nojekyll              # Required! Jekyll ignores _next folder
git init
git add .
git commit -m "Deploy"
git branch -M main
git remote add origin git@github.com:USERNAME/domus.git
git push -f origin main
```

**Important:** The `.nojekyll` file is critical — without it, GitHub Pages' Jekyll processor ignores the `_next` folder.

---

## Sample Data

### Cities
- Berlin, Germany
- Lisbon, Portugal
- Barcelona, Spain

### Properties (Co-Living Houses)
- Mitte Co-Living House (Berlin) - 3 rooms
- Bairro Alto House (Lisbon) - 3 rooms
- Gràcia Townhouse (Barcelona) - 3 rooms

### Sanctuaries
- Alentejo Countryside Retreat (Portugal)
- Mallorca Coastal Sanctuary (Spain)

### Total Listings
18 listings across all cities and types

---

## Brand Guidelines

### Tone
- Calm
- Confident
- Selective
- Editorial
- Never salesy

### Say
- "We curate"
- "We review"
- "We select"

### Avoid
- "Best deals"
- "List your space in minutes"
- "Cheapest"

---

## Future Considerations

### Technical Roadmap
- [ ] Connect to real database (Supabase/AWS)
- [ ] Implement actual search filters
- [ ] Add availability calendar
- [ ] Operator dashboard
- [ ] Booking/enquiry system

### Business Roadmap
- [ ] Expand to new cities based on waitlist demand
- [ ] Launch operator subscription model
- [ ] Sanctuary premium packages
- [ ] Data & demand insights for operators

---

## Development

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npx serve out
```

---

## License

Private - All rights reserved.

---

*Domus Living. We curate. We review. We select.*
