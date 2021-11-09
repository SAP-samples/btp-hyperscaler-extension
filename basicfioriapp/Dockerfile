FROM node:12 AS build-env
ADD . /app/
WORKDIR /app
RUN npm install

FROM gcr.io/distroless/nodejs:12
EXPOSE 5000
COPY --from=build-env /app /app
WORKDIR /app
CMD ["node_modules/@sap/approuter/approuter.js"]
