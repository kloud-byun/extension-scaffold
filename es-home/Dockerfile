# To build: docker build -t es-home .
# To start a container: docker run -p 8080:8080 -d --name test-es-home es-home

# Use node:16 as the base image
FROM node:16 as build

# Set working directory
WORKDIR /home/extension-scaffold/es-home

# Set environment variables
ENV URLPATH=/es/ui \
    DO_INSTALL=true

# Copy the rest of the application
COPY . .

# Set user as node
RUN chown -R node:node /home/extension-scaffold
RUN chmod -R 775 /home/extension-scaffold
USER node

# Run build scripts
RUN npm run maybe-install
RUN npm run resolve-applications
RUN npm install parse5 @types/parse5
EXPOSE 8080
# CMD ["npx", "snowpack", "dev"]
RUN npm run build


# Use httpd:2.4.57-alpine for the deploy stage
FROM httpd:2.4.57-alpine as deploy

# Create the logs directory and give appropriate permissions
RUN mkdir -p /usr/local/apache2/logs/ && chown www-data:www-data /usr/local/apache2/logs/

# Copy built app from the build stage to the deploy stage
COPY --from=build --chown=root:root /home/extension-scaffold/es-home/build /home/extension-scaffold/es-home

# Copy custom httpd.conf to the container
COPY httpd.conf /usr/local/apache2/conf/httpd.conf

# echo GIT_DESCRIBE
RUN echo "${GIT_DESCRIBE}" && echo "${GIT_DESCRIBE}" > /etc/es-ui-git-describe

# Set user for security purposes
USER www-data
