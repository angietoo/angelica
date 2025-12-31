# Angelica Reams Website

Personal website for Angelica Reams - founder, software developer, and creator.

## Setup Instructions

### 1. Add Your Images

Place the following images in the `assets/` folder:

- **profile.jpg** - Your profile photo (the one from your screenshots)
- **ar-monogram.png** - Your AR monogram logo (the signature-style logo)

### 2. Update Substack URLs

In `index.html`, find the JavaScript section at the bottom and update:

```javascript
const SUBSTACK_BLOCKCHAIN = 'https://yourblockchain.substack.com'; // Update this
```

Replace with your actual Blockchain Substack URL.

### 3. Update Contact Information

In the Contact section of `index.html`, verify/update:
- Email address
- LinkedIn URL
- Twitter/X URL

### 4. Deploy

This is a static site and can be deployed to:
- **GitHub Pages** (free)
- **Netlify** (free)
- **Vercel** (free)
- Any static hosting service

## Project Structure

```
AngelicaReams/
├── index.html          # Main website file
├── assets/             # Images and media
│   ├── profile.jpg     # Your profile photo
│   └── ar-monogram.png # AR logo
└── README.md           # This file
```

## Features

- Single-page application with smooth section transitions
- Responsive design for mobile and desktop
- Links to Substack newsletters
- Case studies showcase
- Contact information

## Technical Details

- Pure HTML, CSS, and JavaScript (no build process needed)
- Google Fonts: Playfair Display & Inter
- Responsive breakpoint at 768px
- SVG icons for social links
