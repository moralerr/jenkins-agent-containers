FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

# Use official Node.js image as the base image
FROM node:latest

# If npm is not found, set the PATH variable explicitly
ENV PATH="/usr/local/lib/node_modules/npm/bin:${PATH}"

# Install Angular CLI globally
RUN npm install -g @angular/cli


USER jenkins