# Use Python 3.11 as base image
FROM python:3.11-slim

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy the entire deepagents project
COPY . .

# Install the deepagents package in development mode
RUN pip install -e .

# Change to the research example directory
WORKDIR /app/examples/research

# Install research example dependencies
RUN pip install -r requirements.txt

# Expose the default LangGraph dev server port
EXPOSE 2024


# Default command
CMD ["langgraph", "dev", "--host", "0.0.0.0", "--port", "2024"]
