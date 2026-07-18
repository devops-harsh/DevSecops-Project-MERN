# getting the base node.js base image for the npm 
FROM node:20-alpine AS buildStage

# setup the working dir
WORKDIR /app

# copy the dependencies in the working dir 
COPY package.json package-lock.json* ./

# install the all the dependecies we using the npm ci as we have to keep same version specified in the package-lock.json 
RUN npm install --omit=dev && npm cache clean --force

# copy all the source
COPY . .

# this is production stage
# getting the base node.js base image for the npm 
FROM node:20-alpine AS productionStage

# we are creating the group named as appgroup -s for the system group used for service or application 
# we are creating new user appuser same as system user and assigning the user app user group for securtiy practices 
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# copy the node_module artifact from the buildStage 
COPY --from=buildStage /app/node_modules/ /app/node_modules/

# copying the current source code directory from the first stage
COPY --from=buildStage /app /app/

# install the dumb init 
# dumb init works first as process and become the PID 1 after that dumb-init starts the node as child process and receive the SIGINT and SIGNTERM to the pid 1 to and then laterley he transfer it to nodejs process. it will shutodown the service properly( as save current work and then shutdown after docker stop container_name) and remove the zombie process.
RUN apk add --no-cache dumb-init

# go with the appuser 
USER appuser

# exposing the port 5000 to run 
EXPOSE 5000

ENTRYPOINT ["dumb-init","--", "node"]

CMD ["/app/src/index.js"]