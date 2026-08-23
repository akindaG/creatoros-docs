# Final Demo Guide

This guide keeps the CreatorOS AI presentation focused on the approved Version 1.0 scope.

## Suggested presentation sequence

### 1. Introduce the problem

Creators and small businesses need to manage content, scheduling, analytics and growth decisions across social platforms. CreatorOS AI centralizes those workflows and adds AI-assisted recommendations.

### 2. Show the landing page

Explain the product as an AI-powered Social Growth Intelligence Platform rather than only a scheduler.

### 3. Register and log in

Demonstrate:

- Registration
- Login
- Protected workspace access

### 4. Connect a social account

Open Social Accounts and connect Instagram or Facebook using demonstration credentials.

Explain that V1 supports only Facebook and Instagram.

### 5. Open Content Studio

Demonstrate:

- Image or MP4 upload
- Draft title
- Caption editing
- Platform selection
- Save draft
- Edit draft
- Delete draft if needed

### 6. Use AI Assistant

Generate a caption from a topic and demonstrate:

- Caption generation
- Hashtag suggestions
- Content analysis score
- Improvement suggestions
- Copy or use generated content in Content Studio

Mention that the AI layer uses Ollama with Qwen 3 and has a fallback mode for demonstration reliability.

### 7. Schedule the post

From the saved draft:

- Choose a future date and time
- Select Facebook or Instagram
- Schedule the post
- Open Calendar and confirm the event appears

### 8. Show analytics

Demonstrate:

- Reach
- Likes
- Comments
- Shares
- Engagement rate
- Performance series
- Platform reach
- Top-performing content
- CSV export

### 9. Show Growth Insights

Demonstrate:

- Best posting day and time
- Growth recommendations
- Posting consistency guidance
- Engagement improvement suggestions

### 10. Demonstrate publishing

For the safest final presentation, keep:

```env
SOCIAL_PUBLISH_MODE=simulate
```

Explain that the backend supports a live Meta publishing mode, but simulation mode avoids dependence on Meta approval during the university demonstration.

## Short architecture explanation

```text
Next.js frontend
      |
      v
FastAPI REST API
      |
      +--> PostgreSQL / Supabase
      +--> Supabase Storage
      +--> Ollama / Qwen 3
      +--> Facebook / Instagram via Meta APIs
```

## Features to avoid presenting as V1

Do not present these as implemented V1 capabilities:

- TikTok
- LinkedIn
- YouTube
- Competitor intelligence
- Trend prediction
- Agency dashboard
- Mobile application
- Autonomous agents
- Enterprise multi-tenancy

They belong to the future roadmap.

## Final closing statement

CreatorOS AI helps creators and businesses manage content, generate AI-assisted copy, schedule posts, analyze engagement and receive data-driven growth recommendations from one centralized workspace.
