{
  "project_metadata": {
    "project_name": "insulin-scanner-works",
    "project_category": "Health/Diabetes Tool Landing",
    "description": "Landing page with food scan (nutrition info), insulin calculator, emergency alert, diet tips—all instantly usable.",
    "core_purpose": "Quickly empower diabetics to assess food, calculate insulin doses, handle blood sugar emergencies, and improve diet—no barriers.",
    "target_audience": "Diabetics \u0026 caregivers seeking a digital self-help tool for nutrition \u0026 crisis alerts",
    "key_features": [
      "Food scan w/ nutrition facts",
      "Insulin calculator from glucose",
      "Emergency alert \u0026 contacts",
      "Personalized diet suggestions"
    ]
  },
  "frontend": {
    "pages": [
      {
        "page_name": "Home",
        "route": "/",
        "show_in_nav": false,
        "nav_order": 1,
        "purpose": "Diabetes tool landing: food scanning, insulin dosage, emergency alert, diet tips, all on-screen immediately.",
        "description": "Glassmorphism single-page layout, sticky header, sections stacked vertically with large gaps. Each feature (scan, calculator, alert, suggestions) is a bordered, blurred, elevated card (rounded-2xl) on deep gradient background. Primary CTA styles: pill buttons, subtle hover/press transitions. All text left-aligned except hero/alerts. Emergency section anchor linked for quick scroll. Tablet/mobile: stack vertically, reduce padding, buttons fill width. Z-index separation: header (50), alert (30), others (10).",
        "sections": [
          "Header",
          "FoodScanSection",
          "InsulinCalculatorSection",
          "EmergencyAlertSection",
          "DietSuggestionsSection",
          "Footer"
        ],
        "content_notes": "All sections appear on one scrollable page, separated by large vertical spacing. Transition/animation on key actions (scan, calculate, alert, expand suggestion). No authentication or backend. Data simulated client-side. Images/icons are local static. Header and footer fixed. Emergency section anchor enabled.",
        "data_needs": [
          "nutritionData (from scan)",
          "insulinDose (from calculator)",
          "emergencyContacts (local list)",
          "bloodSugar (input or simulated)",
          "dietSuggestions (static or AI-generated)"
        ],
        "is_protected": false
      }
    ],
    "navigation": {
      "type": "simple",
      "position": "top",
      "style": "glassmorphism",
      "has_logo": true,
      "has_search": false,
      "extra_items": [],
      "links": [
        {
          "label": "Emergency Alert",
          "route": "#emergency"
        }
      ]
    },
    "theme": {
      "design_dna": "LINEAR_MODERN",
      "color_scheme": "dark_glass",
      "primary_color": "#252850",
      "secondary_color": "#E5EAF5",
      "accent_color": "#7D3C98",
      "font_family": "Inter",
      "font_type": "Sans-serif",
      "border_radius": "2xl (24px)",
      "shadow_style": "deep_glass",
      "animation_style": "framer_fade_slide",
      "interaction_style": "animated_glow, smooth_transition"
    },
    "components": [
      {
        "component_name": "Header",
        "type": "layout",
        "description": "Sticky top full-width bar with tool logo (left-aligned, text bold 2xl, accent color), single navigation link (anchor: scroll to Emergency section), padding-x-8, padding-y-6, z-50, bg glassmorphism, shadow-md, Framer fade-in.",
        "used_on": [
          "Home"
        ],
        "purpose": "Branding, quick navigation to emergency alert.",
        "props": [
          "logo_url",
          "nav_links"
        ]
      },
      {
        "component_name": "FoodScanSection",
        "type": "functional",
        "description": "Section in flex row layout with left half: card-surface (glass, rounded-2xl, shadow-lg, padding-8) holding file input (dashed border, center-aligned), scan button (primary solid, pill, hover glow). Right half: nutritional info display in value-cards (4 cards: protein, carbohydrates, fiber, glucose, grid gap-4), bold Inter font, 1.1em for values, labels below each in semi-bold. Drag \u0026 drop support, image preview above input, fade-in entrance animation.",
        "used_on": [
          "Home"
        ],
        "purpose": "Scan food image, display detected nutritional info.",
        "props": [
          "onScan",
          "nutritionData"
        ]
      },
      {
        "component_name": "InsulinCalculatorSection",
        "type": "functional",
        "description": "Stacked glass card below scan section. Large glucose value (XX g, text-4xl, bold, accent), input override field, calculate button (glass, rounded, pulsate on hover). Dosage recommendation result area with bold recommendation, short maintenance note, subtle warning or success icon, bg-neutral-100-glass. Smooth reveal animation on calculation. Layout spacing-y-5, no border.",
        "used_on": [
          "Home"
        ],
        "purpose": "Calculate and show suggested insulin dose based on glucose.",
        "props": [
          "glucoseValue",
          "onCalculate",
          "insulinDose"
        ]
      },
      {
        "component_name": "EmergencyAlertSection",
        "type": "functional",
        "description": "Wide glassmorphism card with red-accented title (uppercase, bold), display blood sugar current value (large, font-bold, text-3xl), field to enter/test value, critical alert badges (dangerous/caution/normal, color-coded), contact button (pill, red bg, white text, hover swipe-in effect), mini contact details under button (small, muted). Sticky if scrolled into view, collapsible transition for additional info.",
        "used_on": [
          "Home"
        ],
        "purpose": "Warn and allow to notify emergency contacts when blood sugar abnormal.",
        "props": [
          "bloodSugar",
          "emergencyContacts",
          "onAlert"
        ]
      },
      {
        "component_name": "DietSuggestionsSection",
        "type": "data_display",
        "description": "Simple glassglo card with heading (font-semibold, accent color), vertical list of diet suggestion chips (glass pills, pastel bg, text-sm, subtle shadow, hover enlarge). Each chip contains food group, emoji, and reason (next line, lighter gray). Animated slideup appearance. Max-width 4xl, margin-x-auto, margin-y-10. Responsive grid if long content.",
        "used_on": [
          "Home"
        ],
        "purpose": "Show diet recommendations to maintain normal glucose.",
        "props": [
          "suggestions"
        ]
      },
      {
        "component_name": "Footer",
        "type": "layout",
        "description": "Full-width glassmorph bar, height 48px, centered copyright (small, mono, light-text), subtle top border, icons for privacy/terms, grid-x-4. No logo. Z-40, fade-in effect.",
        "used_on": [
          "Home"
        ],
        "purpose": "Site info \u0026 minimum legal/terms links.",
        "props": [
          "footer_links"
        ]
      }
    ],
    "layout": {
      "has_header": true,
      "has_footer": true,
      "has_sidebar": false,
      "max_width": "1400px",
      "spacing": "gap-y-12 px-8 py-16 sm:py-20",
      "footer_links": [
        {
          "label": "Privacy Policy",
          "route": "/privacy"
        },
        {
          "label": "Terms of Use",
          "route": "/terms"
        }
      ]
    }
  },
  "backend": {
    "requires_backend": false,
    "apis": [],
    "database": {
      "models": []
    },
    "authentication": {
      "required": false,
      "type": "N/A",
      "providers": [],
      "protected_apis": null
    },
    "external_apis": [],
    "file_storage": {
      "required": false,
      "types": [],
      "max_size": "N/A"
    }
  },
  "technical_specs": {
    "frontend": {
      "framework": "React 18+",
      "routing": "Single route '/' only",
      "styling": "Tailwind CSS 4, with custom glassmorphism classes",
      "state_management": "Local React state, no redux",
      "animation": "Framer Motion for fade/slide, button glow, collapses",
      "dependencies": [
        "react",
        "react-router-dom",
        "tailwindcss@4",
        "framer-motion"
      ]
    },
    "backend": {
      "framework": "N/A",
      "database": "N/A",
      "orm": "N/A",
      "auth": "N/A",
      "dependencies": []
    }
  }
}
