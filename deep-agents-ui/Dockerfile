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


# Expose the development server port
EXPOSE 3000


# Run the development server
CMD ["npm", "run", "dev"]
