{%- if values.runTimes == "java" -%}
{%- if values.runTimeVersion == "openjdk-11" %}
FROM registry.access.redhat.com/ubi8/openjdk-11:1.17
{%- elif values.runTimeVersion == "openjdk-17" -%}
FROM registry.access.redhat.com/ubi8/openjdk-17:1.17
{% endif %}
COPY --chown=185 target/*.jar /deployments/${{values.appName}}.jar
ENV JAVA_APP_JAR="/deployments/${{values.appName}}.jar"
{%- elif values.runTimes == "nodejs" -%}
# More possible license content here
FROM node:${{values.runTimeVersion}}
#create app directory
WORKDIR /usr/src/app
# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./
RUN npm install
# If you are building your code for production
# RUN npm ci --only=production
# Bundle app source
COPY . .
EXPOSE 8080
CMD [ "node", "server.js" ]
{% endif %}
