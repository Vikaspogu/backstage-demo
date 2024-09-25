{%- if values.runTimes == "java" -%}
{%- if values.runTimeVersion == "openjdk-11" -%}
FROM registry.access.redhat.com/ubi8/openjdk-11:1.17
{%- elif values.runTimeVersion == "openjdk-17" -%}
FROM registry.access.redhat.com/ubi8/openjdk-17:1.17
{% endif %}
COPY --chown=185 target/*.jar /deployments/${{values.appName}}.jar
ENV JAVA_APP_JAR="/deployments/${{values.appName}}.jar"
{%- elif values.runTimes == "nodejs" -%}
# Stage 1 - Install dependencies
FROM registry.access.redhat.com/ubi9/nodejs-18-minimal:latest AS deps

COPY package.json package-lock.json ./
RUN npm ci

# Stage 2 - Build the source code
FROM registry.access.redhat.com/ubi9/nodejs-18-minimal:latest AS builder

COPY . .
COPY --from=deps /opt/app-root/src/node_modules ./node_modules

USER root
RUN npm run build

# Stage 3 - Production image, copy all the files and start NodeJS
FROM registry.access.redhat.com/ubi9/nodejs-18-minimal:latest AS runner

COPY --from=deps --chown=1001:1001 /opt/app-root/src .
COPY --from=builder --chown=1001:1001 /opt/app-root/src/dist ./dist

ENV NODE_ENV production

# Switch to nodejs user
USER 1001

# Install production dependencies
RUN npm ci

ENV PORT ${{ values.port }}
EXPOSE ${{ values.port }}

CMD ["npm", "run", "start"]
{% endif %}
