FROM node:24-alpine AS build

RUN apk add unzip wget patch

WORKDIR /src
RUN wget https://github.com/yumata/lampa-source/archive/refs/heads/main.zip && \
unzip main.zip && mv lampa-source-main/* . && rm -rf main.zip lampa-source-main/

COPY lampa.patch .
RUN patch -p1 < lampa.patch

RUN npm install
RUN npm run build

RUN tar -czf /lampa.tar.gz -C /src/build/web .

FROM scratch
COPY --from=build /lampa.tar.gz /