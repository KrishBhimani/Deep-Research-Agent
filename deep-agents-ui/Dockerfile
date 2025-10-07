# Use Node.js 18 as base image
FROM node:18-alpine

# Install system dependencies
RUN apk add --no-cache libc6-compat

# Set working directory
WORKDIR /app

# Copy package files
COPY package.json package-lock.json* ./

# Install all dependencies (including dev dependencies for npm run dev)
RUN npm ci

# Copy the rest of the application
COPY . .

# Copy environment file if it exists
RUN if [ -f .env.example ]; then cp .env.example .env.local; fi

# Expose the development server port
EXPOSE 3000

# Set environment variables
ENV NODE_ENV=development
ENV NEXT_TELEMETRY_DISABLED=1

# Default environment variables (can be overridden)
ENV NEXT_PUBLIC_DEPLOYMENT_URL=https://deepagents-backend-xo-c9qso8j.hello-xo.nl
ENV NEXT_PUBLIC_AGENT_ID=research
ENV NEXT_PUBLIC_AUTH_ENABLED=false

# Run the development server
CMD ["npm", "run", "dev"]
