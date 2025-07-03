# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Commands

### Running the Application
- `npm start` - Starts the application in development mode with nodemon and debug port (0.0.0.0:9229)
- `npm run start-prod` - Starts the application in production mode with node
- Application runs on port 3000

### Development Tools
- Uses nodemon for automatic restarts during development
- Debug port available at 0.0.0.0:9229 for remote debugging

## Architecture Overview

This is a simple Node.js Express application designed as a DevSpace quickstart template:

- **Main Application**: `index.js` - Single-file Express server serving a DevSpace demo page
- **Port**: Application listens on port 3000
- **Dependencies**: Minimal setup with express and nodemon
- **Containerization**: Dockerfile uses Node 13.14-alpine base image

### Key Files
- `index.js`: Main application entry point with single route handler
- `package.json`: Project configuration with start scripts
- `Dockerfile`: Container configuration for deployment
- `package-lock.json`: Dependency lock file

### Application Structure
- Single GET route at `/` serving HTML content
- HTML includes DevSpace branding and quickstart instructions
- No separate static file serving - HTML is inline in the route handler
- No database or external service dependencies