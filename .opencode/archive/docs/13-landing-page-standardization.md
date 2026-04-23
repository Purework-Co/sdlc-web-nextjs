# Landing Page Standardization

This document defines the standardization for building stunning, professional landing pages in this full-stack application using Framer Motion.

## 1. Overview

### 1.1 Purpose
- Define standards for stunning, modern landing pages
- Industry-adaptive design (style changes based on use case)
- Smooth animations and micro-interactions
- Professional, production-ready quality

### 1.2 Tech Stack
- **Animation Library**: Framer Motion (https://www.framer.com/motion/)
- **Installation**: `npm install framer-motion`
- **Framework**: Next.js 14+ with App Router
- **UI Components**: Shadcn UI + custom extensions

## 2. Animation Specifications

### 2.1 Section Transitions (Scroll-Triggered)
Use `whileInView` with `viewport` options for scroll-triggered animations:

```tsx
import { motion } from "framer-motion";

<motion.div
  initial={{ opacity: 0, y: 50 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true, margin: "-100px" }}
  transition={{ duration: 0.6, ease: "easeOut" }}
>
  {/* Section content */}
</motion.div>
```

**Standard timing**:
- Duration: 0.5-0.8s (smooth, not too fast)
- Easing: "easeOut" for natural feel
- Stagger: 0.1s between items in a grid

### 2.2 Hover Animations

#### Scale + Glow Effect
```tsx
<motion.div
  whileHover={{ scale: 1.02, boxShadow: "0 0 20px rgba(59, 130, 246, 0.3)" }}
  transition={{ duration: 0.2 }}
>
```

#### Card Expand Animation
```tsx
<motion.div
  whileHover={{ scale: 1.01 }}
  initial={{ opacity: 0 }}
  whileInView={{ opacity: 1 }}
>
```

### 2.3 Micro-interactions
- Button press: scale(0.98) on tap
- Link hover: underline animation
- Icon hover: subtle rotation or bounce
- Focus states: ring with glow

## 3. Industry-Adaptive Styling

### 3.1 Style System
The landing page design must adapt based on the industry/use case requested. Style is determined at project start.

### 3.2 Predefined Industry Themes

| Industry | Primary Color | Secondary Color | Vibe | Font Style |
|----------|---------------|----------------|------|------------|
| **SaaS** | Blue (#3B82F6) | Violet (#8B5CF6) | Modern, Tech | Inter, sans-serif |
| **E-commerce** | Emerald (#10B981) | Amber (#F59E0B) | Fresh, Active | Poppins, sans-serif |
| **Corporate** | Navy (#1E3A8A) | Slate (#64748B) | Professional | IBM Plex Sans |
| **Portfolio** | Purple (#7C3AED) | Pink (#EC4899) | Creative | Poppins, serif |
| **HealthTech** | Teal (#14B8A6) | Sky (#0EA5E9) | Calm, Trust | DM Sans |
| **FinTech** | Green (#22C55E) | Gold (#EAB308) | Trust, Wealth | Plus Jakarta Sans |
| **EdTech** | Orange (#F97316) | Yellow (#EAB308) | Energetic, Learn | Nunito |
| **Startup** | Indigo (#6366F1) | Cyan (#06B6D4) | Bold, Fresh | Space Grotesk |
| **Real Estate** | Amber (#D97706) | Stone (#78716C) | Premium | Roboto |
| **Restaurant** | Red (#DC2626) | Orange (#EA580C) | Appetizing, Warm | Playfair Display |

### 3.3 Theme Customization
Each theme provides:
- `primaryColor`: Main brand color for CTAs, accents
- `secondaryColor`: Supporting color for highlights
- `background`: Page background (light/dark adaptive)
- `fontFamily`: Primary font family
- `borderRadius`: Rounded corners (4px-16px)
- `animationIntensity`: Subtle/Medium/Bold multiplier

### 3.4 Custom Theme Request
For industries not listed, create custom theme with:
- primaryColor: Brand color (hex)
- secondaryColor: Complementary color (hex)
- fontFamily: Google Fonts compatible
- aesthetic: Minimal/Modern/Classic/Bold/Creative

## 4. Landing Page Sections

### 4.1 Standard Sections (in order)

1. **Navigation Bar**
   - Logo (left)
   - Menu links (center/hidden on mobile)
   - CTA button (right)
   - Transparent -> solid on scroll

2. **Hero Section**
   - Headline (H1): Large, bold
   - Subheadline: Supporting text
   - Primary CTA: "Get Started" / "Sign Up"
   - Secondary CTA: "Learn More"
   - Hero image/graphic (right or background)
   - Animated entrance

3. **Social Proof Bar**
   - "Trusted by" text
   - Company logos (grayscale)
   - Horizontal scroll on mobile

4. **Features/Benefits Section**
   - Section title
   - Feature cards (3-4 columns)
   - Icon + Title + Description
   - Hover effects on cards

5. **How It Works / Process**
   - Step-by-step visual
   - Numbered steps with connecting line
   - Animated reveal on scroll

6. **Testimonials**
   - Quote cards with avatar
   - Name, title, company
   - Star rating
   - Carousel or grid layout

7. **Pricing (if applicable)**
   - Pricing tiers (3 columns)
   - Feature checklist
   - Highlight "Popular" tier
   - CTA buttons

8. **FAQ Section**
   - Accordion style
   - Expand/collapse animation

9. **CTA Section**
   - Final call-to-action
   - Email input or button
   - Strong headline

10. **Footer**
    - Logo
    - Links columns
    - Social icons
    - Copyright

### 4.2 Section Animation Patterns

| Section | Entry Animation | Duration |
|---------|----------------|----------|
| Hero | fadeInUp | 0.8s |
| Features | fadeInUp with stagger | 0.6s |
| Process Steps | fadeInLeft/Right | 0.5s each |
| Testimonials | fadeInUp | 0.6s |
| CTA | scaleIn | 0.5s |
| Footer | fadeIn | 0.4s |

## 5. Component Animation Library

### 5.1 Reusable Animation Variants
Create a shared animation config file:

```tsx
// src/lib/animations.ts
export const fadeInUp = {
  initial: { opacity: 0, y: 30 },
  animate: { opacity: 1, y: 0 },
  transition: { duration: 0.6, ease: "easeOut" }
};

export const staggerContainer = {
  animate: {
    transition: {
      staggerChildren: 0.1
    }
  }
};

export const cardHover = {
  whileHover: { scale: 1.02 },
  transition: { duration: 0.2 }
};
```

### 5.2 Common Animation Presets

```tsx
// Section entrance
<motion.section
  initial={{ opacity: 0, y: 40 }}
  whileInView={{ opacity: 1, y: 0 }}
  viewport={{ once: true }}
  transition={{ duration: 0.6 }}
>

// Card with hover
<motion.div
  whileHover={{ scale: 1.02, boxShadow: "0 10px 40px rgba(0,0,0,0.1)" }}
  transition={{ duration: 0.2 }}
>

// Button press
<motion.button
  whileTap={{ scale: 0.98 }}
  whileHover={{ scale: 1.02 }}
>
```

## 6. Performance Guidelines

### 6.1 Animation Performance
- Use `transform` and `opacity` only (GPU accelerated)
- Avoid animating: `width`, `height`, `top`, `left` (layout changes)
- Use `will-change` sparingly
- Test on mobile devices

### 6.2 Accessibility
- Respect `prefers-reduced-motion`:
```tsx
import { useReducedMotion } from "framer-motion";

const shouldReduceMotion = useReducedMotion();

const animation = shouldReduceMotion
  ? { whileInView: { opacity: 1 } }
  : { whileInView: { opacity: 0, y: 30 } };
```

### 6.3 Loading Strategy
- Use `loading="lazy"` on below-fold images
- Implement skeleton loaders
- Preload critical fonts

## 7. Responsive Behavior

### 7.1 Breakpoints
- Mobile: < 640px
- Tablet: 640px - 1024px
- Desktop: > 1024px

### 7.2 Animation Scaling
- Reduce animation complexity on mobile
- Disable parallax on mobile
- Use simpler transitions

### 7.3 Touch Interactions
- Replace hover with tap on mobile
- Larger touch targets (min 44px)
- No hover-dependent CTAs

## 8. Code Quality Standards

### 8.1 File Organization
```
src/
├── app/
│   └── (marketing)/
│       └── page.tsx          # Landing page
├── components/
│   └── marketing/
│       ├── Hero.tsx
│       ├── Features.tsx
│       ├── Testimonials.tsx
│       └── ...
├── lib/
│   └── animations.ts        # Shared animations
└── config/
    └── themes.ts          # Industry themes
```

### 8.2 Component Structure
```tsx
"use client";

import { motion } from "framer-motion";

interface HeroProps {
  // props
}

export function Hero({ }: HeroProps) {
  return (
    <motion.section>
      {/* content */}
    </motion.section>
  );
}
```

### 8.3 Naming Conventions
- Components: PascalCase (HeroSection, FeatureCard)
- Animations: camelCase with prefix (fadeInUp, cardHover)
- Variants: descriptive (default, hover, active)
- Config: descriptive (themeSaaS, themeEcommerce)

## 9. SEO-Friendly Requirements

### 9.1 Metadata
Every landing page MUST include complete metadata:

```tsx
// app/layout.tsx or app/page.tsx
import { Metadata } from "next";

export const metadata: Metadata = {
  title: "Brand Name | Primary Keyword - Secondary Keyword",
  description: "Compelling description under 160 chars with primary keyword",
  keywords: ["primary keyword", "secondary keyword", "long-tail keyword"],
  authors: [{ name: "Company Name" }],
  creator: "Company Name",
  publisher: "Company Name",
  robots: { index: true, follow: true },

  // Open Graph
  openGraph: {
    type: "website",
    locale: "en_US",
    url: "https://domain.com",
    siteName: "Brand Name",
    title: "Brand Name | Primary Keyword",
    description: "Compelling description under 160 chars",
    images: [
      {
        url: "https://domain.com/og-image.jpg",
        width: 1200,
        height: 630,
        alt: "Brand Name - Primary Keyword"
      }
    ]
  },

  // Twitter Card
  twitter: {
    card: "summary_large_image",
    title: "Brand Name | Primary Keyword",
    description: "Compelling description under 160 chars",
    creator: "@twitter_handle",
    images: ["https://domain.com/og-image.jpg"]
  },

  // Canonical URL
  alternates: {
    canonical: "https://domain.com"
  }
};
```

### 9.2 Semantic HTML
Use proper HTML5 semantic structure:

```tsx
<header>
  <nav aria-label="Main navigation">
    <a href="/">Logo</a>
    <ul>
      <li><a href="#features">Features</a></li>
      <li><a href="#pricing">Pricing</a></li>
    </ul>
  </nav>
</header>

<main>
  <section id="hero" aria-labelledby="hero-heading">
    <h1 id="hero-heading">Main Heading with Primary Keyword</h1>
  </section>

  <section id="features" aria-labelledby="features-heading">
    <h2 id="features-heading">Features Heading</h2>
    <article>Feature card</article>
  </section>

  <section id="testimonials" aria-labelledby="testimonials-heading">
    <h2 id="testimonials-heading">Customer Reviews</h2>
  </section>
</main>

<footer>
  <address>Company address</address>
</footer>
```

### 9.3 Heading Hierarchy
- **H1**: Exactly ONE per page (main value proposition + primary keyword)
- **H2**: Section headings (secondary keywords)
- **H3**: Subsection content (long-tail keywords)
- No skipping levels (H1 → H3 is not allowed)

### 9.4 Core Web Vitals
Optimize for Google's Core Web Vitals:

| Metric | Target | Optimization |
|--------|--------|---------------|
| **LCP** | < 2.5s | Preload hero image, optimize TTFB |
| **FID/INP** | < 100ms | Defer non-critical JS, code splitting |
| **CLS** | < 0.1 | Set explicit image dimensions, reserved space |

### 9.5 Image Optimization
```tsx
import Image from "next/image";

<Image
  src="/hero-image.jpg"
  alt="Descriptive alt text with keyword"
  width={1200}
  height={630}
  priority // For LCP - load above-fold images immediately
  sizes="(max-width: 768px) 100vw, 1200px"
/>
```

### 9.6 Structured Data (Schema.org)
Add JSON-LD for rich search results:

```tsx
// Organization schema
<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{
    __html: JSON.stringify({
      "@context": "https://schema.org",
      "@type": "Organization",
      name: "Company Name",
      url: "https://domain.com",
      logo: "https://domain.com/logo.png",
      sameAs: [
        "https://facebook.com/company",
        "https://twitter.com/company",
        "https://linkedin.com/company"
      ]
    })
  }}
/>

// Product/Service schema (if applicable)
<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{
    __html: JSON.stringify({
      "@context": "https://schema.org",
      "@type": "Product",
      name: "Product Name",
      description: "Product description",
      image: "https://domain.com/product.jpg",
      brand: {
        "@type": "Brand",
        name: "Company Name"
      },
      offers: {
        "@type": "Offer",
        priceCurrency: "USD",
        price: "99.00",
        availability: "https://schema.org/InStock"
      }
    })
  }}
/>

// FAQ schema
<script
  type="application/ld+json"
  dangerouslySetInnerHTML={{
    __html: JSON.stringify({
      "@context": "https://schema.org",
      "@type": "FAQPage",
      mainEntity: [
        {
          "@type": "Question",
          name: "What is...?",
          acceptedAnswer: {
            "@type": "Answer",
            text: "Answer text..."
          }
        }
      ]
    })
  }}
/>
```

## 10. Google EEAT Principles

### 10.1 Overview
EEAT = **Experience, Expertise, Authoritativeness, Trustworthiness**. Critical for YMYL (Your Money or Your Life) pages.

### 10.2 Experience (E)
Demonstrate first-hand experience with the product/service:

- Include real user photos/videos in testimonials
- Show behind-the-scenes content
- Use authentic case studies
- Include "As seen in" or media mentions

### 10.3 Expertise (E)
Show subject matter expertise:

- Include team credentials and certifications
- Add "About Us" page with team bios
- Publish original research/data
- Include author bylines with credentials

```tsx
<address>
  <p>Written by <a href="/about/john-doe">John Doe</a>, PhD in Data Science</p>
  <p>Verified by <a href="/about/jane-smith">Jane Smith</a>, CPA</p>
</address>
```

### 10.4 Authoritativeness (A)
Build authority signals:

- Display client logos (trusted by...)
- Show numbers: users, revenue, years
- Include awards and certifications
- Link to reputable sources
- Press mentions and media coverage

### 10.5 Trustworthiness (T)
Establish trust through:

- Clear contact information (address, phone, email)
- Privacy policy and terms links
- Secure socket layer (HTTPS)
- Transparent pricing (no hidden fees)
- Real, verifiable reviews
- Easy refund/cancellation policies

### 10.6 EEAT Implementation Checklist

| Element | Implementation | Priority |
|---------|--------------|----------|
| Author bio | Team page with credentials | High |
| Contact info | Header/footer with address | High |
| Privacy policy | `/privacy` page linked | High |
| Terms of service | `/terms` page linked | High |
| SSL certificate | HTTPS enabled | High |
| Real testimonials | Verified customer reviews | High |
| Trust badges | Security certifications | Medium |
| Case studies | Detailed success stories | Medium |
| FAQ section | JSON-LD schema | Medium |
| Social proof | Client logos, metrics | High |

### 10.7 Content Guidelines

DO:
- Write original, valuable content
- Include citations with links to authoritative sources
- Update content regularly (show last modified date)
- Answer common questions comprehensively
- Use clear, honest language

DON'T:
- Copy content from other sites
- Exaggerate claims or results
- Hide important information
- Use misleading headlines
- Stuff keywords unnaturally

## 11. Quick Reference Card

| Animation | Use Case | Duration |
|-----------|----------|----------|
| fadeInUp | Section entry | 0.6s |
| fadeInLeft/Right | Side-by-side content | 0.5s |
| scaleIn | Modal, CTA | 0.3s |
| cardHover | Cards, buttons | 0.2s |
| stagger | Grid items | 0.1s delay |
| glow | Focus, active | 0.3s |

---

## Related Documentation

| Document | Purpose |
|----------|---------|
| `08-admin-web-standardization.md` | Admin dashboard standards |
| `frontend-developer.md` | Agent configuration |