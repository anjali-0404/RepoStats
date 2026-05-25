# Folder Structure

This document explains the organization of the RepoStats project.

## Project Overview

```
RepoStats/
├── docs/                    # Documentation files
│   ├── installation.md
│   ├── architecture.md
│   ├── contribution-guide.md
│   ├── deployment.md
│   ├── api.md
│   └── folder-structure.md
├── repostats/               # Main application (Next.js)
│   ├── app/                 # Next.js App Router
│   │   ├── api/             # API routes
│   │   ├── components/        # Re-usable React components
│   │   ├── lib/             # Utility functions
│   │   ├── public/          # Static assets
│   │   ├── package.json
│   │   ├── next.config.mjs
│   │   └── eslint.config.mjs
│   └── README.md
├── .github/                 # GitHub templates and workflows
│   ├── workflows/
│   └── ISSUE_TEMPLATE/
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
└── SECURITY.md
```

## Detailed Application Structure

### `repostats/app/` - Next.js App Router

```
app/
├── api/
│   └── gitstat/
│       └── route.js         # API endpoint for repository stats
├── components/
│   ├── Header.js            # Site header
│   └── Footer.js            # Site footer
├── lib/
│   ├── getAllRepoPages.js   # Pagination utility
│   └── parseRepoUrl.js      # URL parsing utility
├── public/
│   ├── sitemap.xml          # SEO sitemap
│   ├── robots.txt           # Search engine directives
│   └── *.png/html           # Static assets
├── page.js                  # Main page component
├── layout.js                # Root layout
└── globals.css              # Global styles
```

### Key Files Explained

#### `app/page.js`
Main page component that:
- Manages user input state
- Handles API requests
- Displays loading/error states
- Renders charts and statistics

#### `app/api/gitstat/route.js`
API route that:
- Receives POST requests with repository URL
- Fetches data from GitHub API
- Processes and returns formatted statistics

#### `components/Header.js`
Header component with:
- Application title
- Description text
- Visual branding

#### `components/Footer.js`
Footer component with:
- Attribution links
- Additional resources

#### `lib/parseRepoUrl.js`
Utility function that:
- Extracts owner and repo name from GitHub URLs
- Handles various URL formats

#### `lib/getAllRepoPages.js`
Utility function that:
- Handles GitHub API pagination
- Fetches all pages of results automatically

## Configuration Files

| File | Purpose |
|------|---------|
| `package.json` | Dependencies and scripts |
| `next.config.mjs` | Next.js configuration |
| `eslint.config.mjs` | ESLint rules |
| `postcss.config.mjs` | PostCSS configuration |
| `jsconfig.json` | JavaScript path aliases |

## Public Assets

| File | Purpose |
|------|---------|
| `favicon.ico` | Browser tab icon |
| `sitemap.xml` | SEO sitemap |
| `robots.txt` | Search crawler instructions |
| `*.png/html` | Verification files |

## Development vs Production

### Development
- Run `npm run dev` from `repostats/` directory
- Access at `http://localhost:3000`
- Hot reload enabled

### Production
- Run `npm run build` then `npm start`
- Optimized for performance
- No hot reload

## Adding New Files

When adding new files, follow the existing structure:

- **New components**: Add to `app/components/`
- **New API routes**: Add to `app/api/`
- **New utilities**: Add to `app/lib/`
- **Static assets**: Add to `app/public/`
- **Documentation**: Add to `docs/`

## Naming Conventions

- Components: `PascalCase.js` (e.g., `ContributorCard.js`)
- Utilities: `camelCase.js` (e.g., `formatStats.js`)
- API routes: Match endpoint name (e.g., `route.js` in `app/api/gitstat/`)
- Styles: Use Tailwind CSS classes inline