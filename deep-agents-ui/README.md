# Deep Agents UI

Deep Agents are generic AI agents that are capable of handling tasks of varying complexity. This is a UI intended to be used alongside the [`deep-agents`](https://github.com/hwchase17/deepagents?ref=blog.langchain.com) package from LangChain.

If the term "Deep Agents" is new to you, check out these videos!
- [What are Deep Agents?](https://www.youtube.com/watch?v=433SmtTc0TA)
- [Implementing Deep Agents](https://www.youtube.com/watch?v=TTMYJAw5tiA&t=701s)
- [UI Walkthrough](https://youtu.be/0CE_BhdnZZI) for a demonstration of this interface

## Setup Instructions

### Prerequisites
- Node.js 18+ installed on your system
- Backend server running (see the main README.md for backend setup)

### Installation Steps

1. **Install dependencies:**
   ```bash
   # Make sure you're in the deep-agents-ui directory
   npm install
   ```

2. **Set up environment variables:**
   ```bash
   # Make sure you're in the deep-agents-ui directory
   cp .env.example .env.local
   ```

3. **Configure your environment:**

   Edit the `.env.local` file with the appropriate values:

   #### For Local Development:
   ```env
   # URL of your local LangGraph server
   NEXT_PUBLIC_DEPLOYMENT_URL="http://127.0.0.1:2024"
   
   # Your agent ID is the key under "graphs" in the langgraph.json file
   # In the default setup, this is "research"
   NEXT_PUBLIC_AGENT_ID=research
   ```

   #### For Production Deployment:
   ```env
   # URL of your deployed LangGraph server
   NEXT_PUBLIC_DEPLOYMENT_URL="your agent server URL"
   
   # Your agent ID from langgraph.json
   NEXT_PUBLIC_AGENT_ID=research
   
   # Your LangSmith API key (if using LangSmith)
   # Can be obtained from https://smith.langchain.com/
   NEXT_PUBLIC_LANGSMITH_API_KEY=your-langsmith-api-key
   ```

4. **Start the development server:**
   ```bash
   # Make sure you're in the deep-agents-ui directory
   npm run dev
   ```

5. **Access the application:**
   Open [http://localhost:3000](http://localhost:3000) in your browser

## Troubleshooting

If you encounter issues:

- **Cannot connect to backend**: 
  - Ensure the backend server is running
  - Check that NEXT_PUBLIC_DEPLOYMENT_URL is correct
  - Verify there are no network issues or firewalls blocking the connection

- **Agent ID issues**:
  - Make sure you've run the backend server at least once
  - Check the langgraph.json file for the correct agent ID
  - Ensure the NEXT_PUBLIC_AGENT_ID in your .env.local matches the ID in langgraph.json

- **UI not loading properly**:
  - Clear your browser cache
  - Try a different browser
  - Check browser console for any JavaScript errors
