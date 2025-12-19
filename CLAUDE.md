# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a UI prototype collection for an influencer-based restaurant/foodie discovery mobile app called "Stitch Design". The project consists of static HTML mockups representing different screens of the application.

## Technology Stack

- **HTML5** with inline styles and scripts
- **Tailwind CSS** via CDN (`cdn.tailwindcss.com`) with forms and container-queries plugins
- **React 18** via CDN (UMD builds) with React Router DOM 6.3 for the main interactive prototype
- **Babel Standalone** for JSX transformation in browser
- **Google Fonts**: Plus Jakarta Sans, Noto Sans
- **Google Material Symbols** for icons

## Project Structure

Each folder represents a screen mockup:
- `welcome_to_[app_name]/` - Landing page
- `log_in/` - Login screen
- `sign_up/` - Registration screen
- `forgot_password/` - Password reset screen
- `main_feed/` - Social feed with restaurant posts
- `influencer_profile/` - User/influencer profile view
- `restaurant_profile/` - Restaurant detail page with reviews
- `submit_review/` - Review submission form
- `stitch_design_-_influencer_&_foodie_app/` - Complete React SPA prototype with routing

Each folder contains:
- `code.html` - The HTML source
- `screen.png` - Screenshot of the design

## Design System

### Color Palette (Dark Mode)
- Primary: `#ec6d13` (orange)
- Background: `#181411` (dark brown)
- Surface: `#27201c`, `#392f28` (elevated surfaces)
- Border: `#54453b`
- Text muted: `#b9a89d`

### Tailwind Config Extensions
Custom colors are configured via inline `tailwind.config`:
- `primary`, `background-light`, `background-dark`, `surface-dark`, `surface-input`

### Typography
- Font family: `Plus Jakarta Sans` (display), `Noto Sans` (fallback)
- Uses consistent tracking and leading values

## Development Notes

- Files are standalone HTML - open directly in browser to view
- The main React prototype in `stitch_design_-_influencer_&_foodie_app/code.html` includes client-side routing using `MemoryRouter`
- Individual screen mockups are static HTML without JavaScript interactivity
- All external resources load from CDNs - requires internet connection
