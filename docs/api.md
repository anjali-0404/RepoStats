# API Documentation

RepoStats provides a REST API endpoint for fetching repository contribution statistics.

## Base URL

```
http://localhost:3000/api/gitstat
```

For production deployments, replace with your domain.

## Authentication

The API supports optional GitHub Personal Access Token for higher rate limits:

- **Without token**: 60 requests/hour
- **With token**: 5000 requests/hour

Include the token in your request body.

## Endpoints

### POST /api/gitstat

Fetches repository statistics and contributor information.

#### Request

**URL**: `/api/gitstat`

**Method**: `POST`

**Content-Type**: `application/json`

**Body Parameters**:

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `url` | string | Yes | GitHub repository URL (e.g., `https://github.com/facebook/react`) |
| `GITHUB_TOKEN` | string | No | GitHub Personal Access Token |

#### Example Request

```bash
curl -X POST http://localhost:3000/api/gitstat \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://github.com/facebook/react",
    "GITHUB_TOKEN": "ghp_your_token_here"
  }'
```

#### Response

**Success Response** (200 OK):

```json
{
  "success": true,
  "timestamp": "2026-01-15T10:30:00.000Z",
  "repoStats": {
    "totalContributors": 1500,
    "activeContributors": 800,
    "totalContributions": 50000,
    "avgContributionsPerUser": 33.33,
    "topContributor": "gaearon",
    "repoUrl": "https://github.com/facebook/react",
    "owner": "facebook",
    "repoName": "react",
    "lastUpdated": "2026-01-15T10:30:00.000Z"
  },
  "authorStats": {
    "totalAuthors": 400,
    "totalMergedPRs": 2500,
    "authorsWithPRs": 400,
    "avgPRsPerAuthor": 6.25,
    "prDistribution": {
      "novice": 200,
      "active": 120,
      "core": 50,
      "expert": 30
    }
  },
  "contributors": [
    {
      "username": "gaearon",
      "avatar": "https://avatars.githubusercontent.com/u/810438?v=4",
      "contributions": 500,
      "mergedPRs": 50,
      "githubUrl": "https://github.com/gaearon",
      "type": "User"
    }
  ],
  "charts": {
    "topContributors": [
      {
        "name": "gaearon",
        "contributions": 500,
        "mergedPRs": 50,
        "username": "gaearon"
      }
    ],
    "prDistribution": [
      { "name": "1-3 PRs", "value": 200 },
      { "name": "4-10 PRs", "value": 120 },
      { "name": "11-30 PRs", "value": 50 },
      { "name": "31+ PRs", "value": 30 }
    ],
    "activityTrend": [
      {
        "month": "Contributor 1",
        "contributions": 500,
        "prs": 50
      }
    ]
  },
  "metadata": {
    "apiVersion": "1.0",
    "dataPoints": {
      "contributors": 800,
      "pullRequests": 3000,
      "mergedPRs": 2500
    },
    "note": "Contributors with 0 merged PRs have been filtered out"
  }
}
```

#### Error Responses

**Invalid URL** (400 Bad Request):
```json
{
  "success": false,
  "error: "Failed to parse repository URL",
  "details: "Invalid GitHub URL format",
  "timestamp": "2026-01-15T10:30:00.000Z"
}
```

**Repository Not Found** (500 Internal Server Error):
```json
{
  "success": false,
  "error": "An error occurred while processing the request.",
  "details": "404 Not Found",
  "timestamp": "2026-01-15T10:30:00.000Z"
}
```

**Rate Limit Exceeded** (500 Internal Server Error):
```json
{
  "success": false,
  "error": "An error occurred while processing the request.",
  "details": "API rate limit exceeded",
  "timestamp": "2026-01-15T10:30:00.000Z"
}
```

## Response Fields

### repoStats

| Field | Type | Description |
|-------|------|-------------|
| `totalContributors` | number | Total number of contributors in the repository |
| `activeContributors` | number | Contributors with at least 1 merged PR |
| `totalContributions` | number | Sum of all contribution counts |
| `avgContributionsPerUser` | number | Average contributions per active contributor |
| `topContributor` | string | Username of the contributor with most commits |
| `repoUrl` | string | Full GitHub repository URL |
| `owner` | string | Repository owner username |
| `repoName` | string | Repository name |
| `lastUpdated` | string | ISO timestamp of when data was fetched |

### authorStats

| Field | Type | Description |
|-------|------|-------------|
| `totalAuthors` | number | Unique PR authors |
| `totalMergedPRs` | number | Total merged pull requests |
| `authorsWithPRs` | number | Authors with at least 1 merged PR |
| `avgPRsPerAuthor` | number | Average merged PRs per author |
| `prDistribution` | object | Count of authors by PR category |

### Contributor Object

| Field | Type | Description |
|-------|------|-------------|
| `username` | string | GitHub username |
| `avatar` | string | URL to user's avatar |
| `contributions` | number | Total contribution count |
| `mergedPRs` | number | Number of merged pull requests |
| `githubUrl` | string | Link to GitHub profile |
| `type` | string | User type (User/Organization) |

### Charts Data

- `topContributors`: Array of top 10 contributors for bar charts
- `prDistribution`: Data for pie chart showing PR categories
- `activityTrend`: Data for line chart showing contributor activity

## Rate Limiting

### Without Authentication

- **Limit**: 60 requests per hour per IP
- **Reset**: Hourly

### With GitHub Token

- **Limit**: 5000 requests per hour
- **Reset**: Hourly

### Handling Rate Limits

1. Cache responses when possible
2. Use a GitHub token for production usage
3. Implement exponential backoff for retries
4. Show user-friendly error messages

## Error Handling Best Practices

When integrating with this API:

```javascript
try {
  const response = await fetch('/api/gitstat', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ url: repositoryUrl })
  });
  
  const data = await response.json();
  
  if (!data.success) {
    throw new Error(data.details || data.error);
  }
  
  // Process successful response
  console.log(data.contributors);
} catch (error) {
  console.error('API Error:', error.message);
}
```