# Project Architecture

RepoStats is a Next.js application that analyzes GitHub repository contributions using the GitHub REST API.

## High-Level Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Frontend      │────▶│   API Route     │────▶│   GitHub API    │
│   (React)       │     │   (Next.js)     │     │   (REST)        │
└─────────────────┘     └─────────────────┘     └─────────────────┘
        │                       │                       │
        ▼                       ▼                       ▼
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Charts &      │     │   Data          │     │   Repository    │
│   Visualizations│     │   Processing    │     │   Data          │
└─────────────────┘     └─────────────────┘     └─────────────────┘
```

## Core Components

### Frontend Layer

**Page Component** (`app/page.js`)
- Main entry point for the application
- Handles user input (repository URL, GitHub token)
- Manages application state (loading, data, errors)
- Renders the main UI with charts and statistics

**Header Component** (`components/Header.js`)
- Displays the application title and description
- Provides visual branding

**Footer Component** (`components/Footer.js`)
- Contains links and additional information
- Displays attribution

### API Layer

**GitStat API** (`app/api/gitstat/route.js`)
- Serverless API endpoint that handles POST requests
- Parses repository URLs to extract owner and repo names
- Fetches contributor data from GitHub API
- Fetches pull request data to calculate merged PR counts
- Processes and filters data for active contributors
- Returns structured JSON response with statistics and chart data

### Library Layer

**parseRepoUrl** (`lib/parseRepoUrl.js`)
- Utility function to extract owner and repository name from GitHub URLs
- Supports various GitHub URL formats

**getAllRepoPages** (`lib/getAllRepoPages.js`)
- Handles GitHub API pagination automatically
- Fetches all pages of results for endpoints that return paginated data
- Supports authentication via GitHub token

### Data Flow

```
1. User enters repository URL
         │
         ▼
2. Frontend sends POST request to /api/gitstat
         │
         ▼
3. API route parses URL and fetches data from GitHub API
         │
         ├──▶ GET /repos/{owner}/{repo}/contributors
         └──▶ GET /repos/{owner}/{repo}/pulls?state=closed
         │
         ▼
4. API processes data:
   - Calculates merged PR counts per contributor
   - Filters contributors with 0 merged PRs
   - Computes statistics and chart data
         │
         ▼
5. API returns JSON response to frontend
         │
         ▼
6. Frontend renders charts and statistics
```

## Tech Stack

| Layer | Technology | Version |
|-------|------------|---------|
| Framework | Next.js | 16.1.4 |
| Frontend | React | 19.2.3 |
| Charts | Recharts | 3.6.0 |
| Charts | Chart.js | 4.5.1 |
| Styling | Tailwind CSS | 4 |
| Icons | Lucide React | 0.562.0 |
| HTTP Client | Axios | 1.13.2 |
| Linting | ESLint | 9 |

## Data Processing

### Contributor Data

The application fetches contributor data from GitHub and enriches it with:

- Total contributions count
- Merged pull request count
- Avatar URL
- GitHub profile link
- Author type (User/Organization)

### Statistics Calculated

- Total contributors
- Total contributions
- Average contributions per user
- Total merged PRs
- PR distribution categories:
  - Novice: 1-3 PRs
  - Active: 4-10 PRs
  - Core: 11-30 PRs
  - Expert: 31+ PRs

### Filtering Logic

Contributors are filtered to show only those with at least one merged PR, sorted by merged PR count in descending order.

## Error Handling

- API errors are caught and returned with status codes
- Network errors are handled gracefully
- Invalid repository URLs return appropriate error messages
- Rate limiting errors are communicated to the user

## Performance Optimizations

- Parallel fetching of contributors and pull requests using `Promise.all`
- Automatic pagination handling in `getAllRepoPages`
- Efficient data processing with `reduce` and `map` operations