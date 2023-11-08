# Use an official Node.js runtime as a parent image
FROM node:20.5.0-slim 

# add maintainer label
LABEL maintainer="moyu597"

# create a non root user
RUN useradd frontend && mkdir /app && chown frontend:frontend /app

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of your application code to the container
COPY . .

# Expose the port your frontend runs on within the container (e.g., 3000)
EXPOSE 3000


# Add user backend
USER frontend

# Command to run your frontend application
CMD ["npm", "start"]
