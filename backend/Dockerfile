# Use an official Node.js runtime as a parent image
FROM node:20.9.0-slim


# add maintainer label
LABEL maintainer="moyu597"

# create a non root user
RUN useradd backend && mkdir /app && chown backend:backend /app

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json to the container
COPY package*.json ./

# Install application dependencies
RUN npm install

# Copy the rest of your application code to the container
COPY . .

# Expose the port your application will listen on
EXPOSE 4000

# Add user backend
USER backend
 
ENV MONGO_URI=""
# Command to run your Node.js application
CMD [ "npm" ,"start"]
