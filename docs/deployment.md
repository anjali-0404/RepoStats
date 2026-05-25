# Deployment Guide

This guide covers deploying RepoStats to various platforms.

## Prerequisites

- Node.js 16+ locally for building
- GitHub account for Vercel deployment
- Account on your chosen deployment platform

## Build Process

### 1. Install Dependencies

```bash
cd repostats
npm install
```

### 2. Build for Production

```bash
npm run build
```

This creates a `.next/` directory with optimized production files.

### 3. Environment Variables

Create `.env.local` in the `repostats/` directory:

```env
# Optional: GitHub token for higher API limits
GITHUB_TOKEN=your_token_here
```

## Deployment Options

### Vercel (Recommended)

Vercel is the creators of Next.js and provides optimal deployment.

#### One-Click Deploy

1. Fork the repository
2. Sign in to [Vercel](https://vercel.com)
3. Click "New Project"
4. Import your fork
5. Configure:
   - **Framework Preset**: Next.js
   - **Root Directory**: `repostats`
   - **Build Command**: `npm run build`
   - **Output Directory**: `.next`

#### Using Vercel CLI

```bash
npm install -g vercel
cd repostats
vercel
```

#### Environment Variables on Vercel

Add via Vercel Dashboard:
1. Go to Project Settings > Environment Variables
2. Add `GITHUB_TOKEN` (optional)

### Netlify

1. Fork the repository
2. Sign in to [Netlify](https://app.netlify.com)
3. Click "New site from Git"
4. Connect your fork
5. Configure:
   - **Build command**: `npm run build`
   - **Publish directory**: `.next`
   - **Base directory**: `repostats`

### Docker

Create a `Dockerfile` in the `repostats/` directory:

```dockerfile
FROM node:18-alpine AS base

# Install dependencies
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm ci

# Build the app
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json
COPY --from=builder /app/public ./public
EXPOSE 3000
CMD ["npm", "start"]
```

Build and run:

```bash
cd repostats
docker build -t repostats .
docker run -p 3000:3000 repostats
```

### Traditional Hosting (cPanel/VPS)

1. Build locally: `npm run build`
2. Upload `.next/`, `node_modules/`, `package.json`, and `public/` to server
3. Install dependencies on server: `npm install --production`
4. Start: `npm start`

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `GITHUB_TOKEN` | No | GitHub Personal Access Token for higher API rate limits |

### Setting Environment Variables

**Vercel**: Project Settings > Environment Variables

**Netlify**: Site Settings > Build & Deploy > Environment

**Docker**:
```bash
docker run -p 3000:3000 -e GITHUB_TOKEN=ghp_xxx repostats
```

**Linux/Mac**:
```bash
export GITHUB_TOKEN=ghp_xxx
npm start
```

## Custom Domain

### Vercel

1. Go to Project Settings > Domains
2. Add your domain
3. Configure DNS records as instructed

### Other Platforms

Configure DNS to point to your deployment URL.

## Post-Deployment

### Verify Deployment

1. Visit your deployed URL
2. Test with a repository URL (e.g., `https://github.com/facebook/react`)
3. Check browser console for errors

### Monitor

- Check deployment logs for errors
- Monitor GitHub API rate limits
- Review error reports

## Troubleshooting

### Build Fails

```bash
# Clear cache
rm -rf .next node_modules
npm install
npm run build
```

### API Errors

Ensure `GITHUB_TOKEN` is set correctly and not expired.

### CORS Issues

The API route handles CORS automatically through Next.js response handling.

### Memory Issues

For large repositories, increase Node.js memory:

```bash
NODE_OPTIONS="--max-old-space-size=4096" npm run build
```

## Security Considerations

- Never commit `.env.local` with real tokens
- Use environment variables for sensitive data
- Review GitHub token permissions (use minimal scope)
- Enable HTTPS for production deployments