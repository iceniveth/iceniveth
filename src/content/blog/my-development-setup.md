---
title: 'My Development Setup'
description: 'The development environment I make to start building'
pubDate: 'Sept 15 2025'
# heroImage: '../../assets/blog-placeholder-3.jpg'
---

As a software developer since 2016, I've refined my development workspace setup over the years. As of 2025, this is my carefully curated development environment. I work primarily with two machines - a PC and a laptop, both running Windows.

## Windows Subsystem for Linux

When Microsoft released Windows Subsystem for Linux (WSL), I enthusiastically adopted it once it reached stability. Previously, I had Ubuntu as a dual-boot option, but hardware limitations with my trackpad led me to seek alternatives.

My journey with WSL began with a Firebase project that required specific C++ dependencies (during the Node.js v8-v10 era). Initially, I had developed this project on my old Windows laptop where everything worked perfectly. However, when a client reported a bug a year later, I needed to set up the project on my new machine. The process was fraught with C++ compilation errors on Windows, turning what should have been a simple bug fix into hours of dependency troubleshooting. Out of frustration, I decided to try setting up the project in WSL (Ubuntu) - and to my delight, everything installed flawlessly.

I was joining a new company which they were using Elixir/Erlang. Most of the devs were in Mac or Linux. Because I was a Windows guy, nobody from the team had encountered errors when installing Erlang. I've sent about hour(s)/day(s) making it work. Fast-forward to a couple of months, we had new members (Windows) coming in to our team and they all struggled for that dependency. I felt their frustration and as a result, I started to document how to properly install it. A year later, I'll just straight away setup this project in WSL.

## Git

While it's possible to develop software without version control, Git has become an indispensable tool that every developer should master from day one. It eliminates common development headaches such as:

- "Why isn't my code working? It was fine yesterday!"
- "My hard drive crashed, and I lost all my code changes."
- "Here are the files I modified - please manually update your copies."

[Git](https://git-scm.com/) lets us track of code changes and keeps a change history. This also makes it easy to synchronize changes across teams when integrated with a remote repositories like [Github](https://github.com/).

## SSH Key

We had this project in Elixir wherein ProjectB requires ProjectA as a dependency. This dependency is a URI pointing to a private remote repository. Whenever we ran the install command, it would try to install ProjectA but this requires authorization because it's located from a private repository. The only way to make this work is for developers must clone this ProjectA via SSH. This was my start for me learning it. Was actually pretty simple, we just execute a command:

```bash
ssh-keygen -t ed25519 -C "your comment for which this key is for"
```

And then add this on tp the remote repository, like in the case of Github its in [Settings Keys](https://github.com/settings/keys).

Once that has been saved, we can now clone via SSH option.

## Docker

Projects would typically need a database, caching, and some webhook handler. Apps that I would usually use is Postgresql, Redis, and Ngrok. Older projects depends on older versions of those apps. Newer projects depends on the newer versions. To make my life easier dealing with these is I spin up Docker containers for these apps.

```bash
# Run Postgres:14.8 on port 5432
docker run -d --name postgres_14_8 -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres  --restart unless-stopped -v postgres_14_8:/var/lib/postgresql/data postgres:14.8

# Run Postgres:16.10 on port 5433
docker run -d --name postgres_16_10 -p 5433:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres  --restart unless-stopped -v postgres_16_10:/var/lib/postgresql/data postgres:16.10
```

With the above, I was able to run multiple Postgres with different versions. Each of my projects would just use a database connection of a different port whichever versions it would require.

Same would apply to Redis:

```bash
docker run --restart unless-stopped --name redis -v redis_7_2:/data -d -p 6379:6379 redis:7.2
```

I use [Ngrok](https://ngrok.com/) as a webhook handler. Allowing 3rd party services to access my localhost. Create an Ngrok account, then copy the [AuthToken](https://dashboard.ngrok.com/get-started/your-authtoken), and make sure to generate a [static domain](https://ngrok.com/blog-post/free-static-domains-ngrok-users) to avoid getting random URL. I would only start Ngrok in a new separate terminal whenever there is a need for 3rd party service to pass their payload to the APIs.

```bash
docker run --rm -it -p 4040:4040 -e NGROK_AUTHTOKEN=<replace-with-auth-token> ngrok> ngrok/ngrok http --url=<replace-with-the-static-domain-> host.docker.internal:<replace-with-the-localhost-application-port>
```

Another alternative option to Ngrok is [Cloudflare Tunnel](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)

## Code Formatters

My projects now a days involves using Javascript/Typescript. The first thing I commit to the project is enforcing code style consistency. In JS ecosystem, the most popular tool is [Prettier](https://prettier.io/), it fairly easy to set it up. Then with the code editor I use (VSCode), I install the [VSCode Extension](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
I pair this with project level VSCode setting which includes format on save:

```jsonc
// /.vscode/settings.json

{
  "editor.formatOnSave": true,
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}
```

So that everytime I save my changes it automatically formats the code. When I save changes with syntax errors, it wouldn't format it. That gives me a signal that there is something wrong with the code.

Some developers would go deeper by also integrating [ESLint](https://eslint.org/) + Git Hooks. What it does is format the code and statically analyzes code for problems whenever we git commit. I am not a fan of it due to ESLint is too slow. I like to bring up the joke of: _In case of fire, 1. git commit, 2. git push, 3. leave buidling_. With the Eslint + Git Hooks setup, you'll be stuck at #1 waiting for ESLint and be burned.

I've heard of [Biome](https://biomejs.dev/) as an alternative but haven't really used to well.
