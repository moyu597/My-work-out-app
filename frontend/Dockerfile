# Use an official Node.js runtime as a parent image
FROM node:20.9.0-slim

# add maintainer label
LABEL maintainer="moyu597"

# Set the working directory in the container
WORKDIR /app

# Copy all files
COPY . .

# Install application dependencies
RUN npm install

# Expose the port your frontend runs on within the container (e.g., 3000)
EXPOSE 3000

# Command to run your frontend application
CMD ["npm", "start"]
