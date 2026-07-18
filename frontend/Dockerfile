# build stage 
# getting the base image 
FROM node:20-alpine AS buildStage

# setting up the working directory as /app
WORKDIR /app

# copy the package.json to the /app 
COPY package*.json ./

# run the npm install to install dependencies
RUN npm install && npm cache clean --force

# copy the source code to workdir (/app)
COPY . .

# build the binary artifacts 
RUN npm run build

# production stage

# get the nginx base image alias as productionStage
FROM nginx:1.27-alpine AS productionStage

# removing the default configuration and file inside /usr/share/nginx/html which is defualt nginx html page
RUN rm -rf /etc/nginx/conf.d/default.conf   /usr/share/nginx/html/* 

# copy the our nginx.conf to the defualt.conf
COPY nginx.conf  /etc/nginx/conf.d/default.conf     

# copy the artifact from the build stage to the /html where site is hosted
COPY --from=buildStage /app/dist/ /usr/share/nginx/html/

# security practices (running the application as nginx user as if root user run the application and container get hacked and get access of entire system )
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown nginx:nginx /var/run/nginx.pid

# go with the user nginx 
USER nginx

# expose the port 80 
EXPOSE 80

# running the nginx
CMD ["nginx","-g", "daemon off;"]

