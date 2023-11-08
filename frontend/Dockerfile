# Use an official Node.js runtime as a parent image
FROM node:20.5.0-slim

# Add maintainer label
LABEL maintainer="moyu597"

# Create a non-root user
RUN useradd frontend

# Create the /app directory and set its permissions
RUN mkdir /app && chown frontend:frontend /app

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install application dependencies
RUN npm install

# Create the /app/node_modules/.cache directory and set its ownership
RUN mkdir -p /app/node_modules/.cache && chown -R frontend:frontend /app/node_modules/.cache

# Copy the rest of your application code to the container
COPY . .

# Expose the port your frontend runs on within the container (e.g., 3000)
EXPOSE 3000

# Add user frontend
USER frontend

# Command to run your frontend application
CMD ["npm", "start"]
