---
layout: post
title: "Adding a Seed File to Populate Dockerized PostgreSQL"
description: "Adding a seed file to populate PostgreSQL Docker"
tags: ["docker", "PostgreSQL"]
---

I was trying to figure out how to seed my PostgreSQL database in my dockerized application. It's actually very straightforward. You can simply put all your `*.sql ` file inside `/docker-entrypoint-initdb.d/`.

In my `docker-compose.yml`, I linked my `db/seed.sql` file inside my docker PostgreSQL image through the `volumes` tag. It looks like this:

    postgres:
      image: postgres:latest
      volumes_from:
        - data
      volumes:
        - ./db/seed.sql:/docker-entrypoint-initdb.d/seed.sql
      ports:
        - "5432:5432"
      env_file: .env

The file `seed.sql` is then used to populate PostgreSQL.

Docs [here](https://hub.docker.com/_/postgres/).
