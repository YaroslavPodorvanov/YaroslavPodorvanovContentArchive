---
source: reddit
url: https://www.reddit.com/r/golang/comments/1rpz4b7/
title: "u8views – open-source GitHub profile view counter"
date: 2026-03-10T14:55:06.764Z
---

Hi, in 2023 my team and I built a GitHub profile view counter to add to our resumes (among other reasons). At the time, most existing counters simply showed an absolute total number of views for all time. We aggregated views by hour to display a monthly views graph instead.

The project is written in Go, PostgreSQL, sqlc and goose. It runs on a cheap $5 VPS in docker-compose. HTTPS certificates are handled via the official experimental \`autocert\` package to keep things simple — no Nginx, no extra steps. Ports 80 and 443 are exposed directly to the network, which is a conscious tradeoff to keep the deployment as minimal as possible.

I tested the project on this same $5 VPS to understand its capacity: the DB can handle 10,000 unique users every hour for a year — around 87,600,000 records annually — after which I can clean it up. Each record represents one user per hour with a view count field, so multiple views within the same hour are aggregated into a single row. Currently it's around 35,000 records per month and 580,000 total. The full DB is 62 MB, of which the profile\_hourly\_views\_stats table takes 52 MB (30 MB data, 21 MB index).

The project requires authorization to verify that a GitHub profile actually exists and to avoid being used as a generic counter that would fill the DB quickly. Authorization uses no email — only public data: GitHub user ID and username.

There's a static ad banner — the goal is to cover hosting costs.

All views are counted because all requests from GitHub are proxied through Camo, so my server has no data to determine uniqueness.

Website with setup instructions: [https://u8views.com/](https://u8views.com/)  
Repository: [https://github.com/u8views/go-u8views](https://github.com/u8views/go-u8views)

Happy to answer questions about the implementation.
