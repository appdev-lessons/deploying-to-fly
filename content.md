# Deploying to Fly.io

* Table of Contents
{:toc}

Any time you want to deploy a static website, we recommend using our [HTML template](https://learn.firstdraft.com/lessons/60#html-template), and [GitHub Pages](https://learn.firstdraft.com/lessons/55). 

But there's another option that will also work, and work for dynamic database-backed CRUD apps: [Fly.io](https://fly.io). 

Use your GitHub account [to sign-up](https://fly.io/app/sign-up), and let's see how we can serve a variety of apps so that anyone with a URL can visit your projects.

Read the next section about entering credit card information (you won't be charged!) then visit the appropriate deployment lesson for your app in the documentation below:

* [a dynamic non-database Sinatra app](#deploying-a-dynamic-sinatra-app){:target="_self"}
* [a database-backed CRUD Sinatra app](#deploying-a-crud-sinatra-app){:target="_self"}
* [a database-backed CRUD Rails app](#deploying-a-crud-rails-app){:target="_self"}

## Fly Offering and Payment

Fly's free usage tier (their ["Hobby Plan"](https://fly.io/docs/about/pricing/)), provides three 256MB RAM Virtual Machines, and 3GB of volume storage, which is enough to host your side projects. 

Note that after you sign-up, you will need to **enter credit card information [in the billing section](https://fly.io/dashboard/personal/billing)** of your dashboard (navigate to `fly.io/dashboard` and see the "Billing" tab). You **will not be charged** for making small side projects, but the credit card must be on file. And currently Fly does not collect monthly bills totaling less than $5, if you do happen to incur some small costs in your usage.

If you decide to host larger Rails apps, and want something more powerful to serve real customers, then you can easily scale Fly up by increasing the CPUs and RAM available and pay as you go in CPU usage hours. Alternatively, you may end up going with a service like [Heroku](https://www.heroku.com/) or [hatchbox.io](https://hatchbox.io/). But, the Fly.io hobby plan should be sufficient (and free!) for now.

### Aside: How does Fly work?

Unlike GitHub Pages, which is serving the static files found in a given repository's root directory, or services like [Hatchbox](https://hatchbox.io/), which copy all of your code to a server and run the app similar to how you might in a Codespace environment, Fly uses [Docker images](https://www.docker.com/) run on "Virtual Machines".

You don't need to worry about the details, but essentially, Fly creates a "recipe" in a `Dockerfile` and some additional runtime settings in a `fly.toml` file. The recipe and runtime settings tell the three free machines that Fly provides how exactly to create a mini-computing environment to run a server (similar to our `bin/dev` command) and potentially connect to a database.

In practice, deploying to Fly boils down to just a couple of simple commands: `fly launch` + `fly deploy`.

We have pre-installed Fly in both the Codespaces and Gitpod environment, but you can also [find easy instructions to do so locally](https://fly.io/docs/hands-on/install-flyctl/).

## Deploying a Dynamic Sinatra App

Let's begin with the simplest example: a Sinatra app built up from the [Ruby template](https://learn.firstdraft.com/lessons/60#ruby-template), or using the [Sinatra template](https://learn.firstdraft.com/lessons/60#sinatra-template) that only has a few pages that are dynamic (i.e. modified in the backend by some Ruby code), but do not have a database attached.

In the Codespace of your project, login to fly by typing this at the terminal, and clicking "Open" when prompted:

```
fly auth login
```

<!-- ![](/assets/fly-1-auth-login.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686241375/fly-1-auth-login_scrvsc.png)

<!-- ![](/assets/fly-1-auth-login-open-page.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686241498/fly-1-auth-login-open-page_ot2odm.png)

You may need to sign in again with your GitHub account. Then on the fly.io page that opens, click "Continue as [your-username]". You should then be prompted to close the browser tab. Do that and return to your workspace terminal.

Now, at the terminal, run:

```
fly launch
```

You will be taken through a series of steps to setup the app. 

First, choose a unique name that makes sense for your app:

<!-- ![](/assets/fly-2-name.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686241691/fly-2-name_ts2k3p.png)

Next, use the arrow keys to select the appropriate timezone (you can also begin typing a city/state/country name and it will filter the list) and press <kbd>return</kbd> to select it:

<!-- ![](/assets/fly-3-timezone.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686111337/fly-3-timezone_fvbwnt.png)

For the next question about a `Postgresql database`, type `N` for "no", since we are just deploying a static database-free app for now. 

You will also be prompted to setup a `Redis database`. Say `N` for "no".

If you are asked to create a `.dockerignore` file, say `Y` for "yes".

<!-- ![](/assets/fly-4-database.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686111337/fly-4-database_bjyjdp.png)

When you finish, you will see `Your Ruby app is prepared for deployment`. 

The `fly launch` command created some new files in your repository: `.dockerignore` (only if a `.gitignore` was already present), `Dockerfile`, and `fly.toml`. These files are the instructions that Fly will follow to create and serve your app on your virtual machines hosted by Fly.io.

This is a great time to make a [git commit with the message "fly launch success"](https://learn.firstdraft.com/lessons/50#committing-changes).

There is just one more command to run to deploy the app:

```
fly deploy --ha=false
```

(Note: the `--ha=false` --- "high availability" --- flag will prevent Fly from starting two virtual machines, thus saving you space for other projects, but making your app less snappy. You can optionally leave it off.)

That command will take a minute or two and there will be a lot of output at your terminal. Fly is building a docker image and pushing it to the server, and then the server is launching. Wait patiently until you are returned to your empty command-line.

When it is done, you can launch into the deployed app by visiting the URL `https://<your-app-name>.fly.dev`, or by simply entering:

```
fly open
```

At any time you can visit your app at [fly.io/dashboard](https://fly.io/dashboard). There you will find tabs to monitor the server logs (similar to `bin/dev`!) and more.

And that's it! Your dynamic website is live and you can share that link with anyone.

## Deploying a CRUD Sinatra App

Let's consider the Fly deployment case for a dynamic database-backed CRUD Sinatra app, like one you might build up from the [Sinatra template](https://learn.firstdraft.com/lessons/60#sinatra-template).

The steps to deploy on Fly once again boil down to: `fly launch` + `fly deploy`, with a few modifications of the files.

First:

```
fly auth login
```

Then:

```
fly launch
```

Give the app a unique name, choose a timezone, then walk through the rest of the questions.

* "N" (No) for creating a `Postgresql database`, 
* "N" for creating a `Redis database`, and 
* "Y" (Yes) for creating a `.dockerignore`.

When you finish, you will see `Your Ruby app is prepared for deployment`. 

Now comes a key part. We don't want to move our `db/development.sqlite3` database to the deployed server, but we _do_ need a database there so that visitors can CRUD.

Open the newly generated `.dockerignore` file and add this to the end of it:

```bash
# ignore your local database
db/development.sqlite3
```

That will prevent Fly from sending your `db/development.sqlite3` to the virtual machine when you deploy.

But we do need a database! We also want the database to persist whenever the server restarts or we redeploy our app. Fly will completely rebuild the app anytime that happens, including wiping out any data that lives in the application root folder. The way we can get around this [is by using a volume](https://fly.io/docs/reference/volumes/) to create a persistent storage location that we can connect to a database that _won't_ get destroyed when the docker image rebuilds on server restarts.

To create a volume, and point the database to it, first make sure that the `config/environment.rb` file has the line:

```ruby
  # setup a database connection
  set(:database, {adapter: "sqlite3", database: ENV.fetch('DATABASE_LOCATION', 'db/development.sqlite3')})
```

That `ENV.fetch(...)` will tell the app to either use the environment variable `'DATABASE_LOCATION'` if it exists, or fall back on our `db/development.sqlite3` database if it doesn't.

Now open the `fly.toml` file generated during the `fly launch` command and add the following to the bottom of the file:

```bash
[mounts]
  source = "production_db"
  destination = "/app/db/production_db"

[env]
  DATABASE_LOCATION = "/app/db/production_db/production.sqlite3"
```

The first `[mounts]` section tells Fly to create a new persistent storage volume called `production_db` in the `db/` folder. The second `[env]` section tells Fly to set a new `ENV` variable, which will be passed to the app when it is time to setup a database connection in the `config/environment.rb` file. Note that we put this new database inside the persistent storage volume `db/production_db/`, and we named it `production.sqlite3`. That differentiates it from our `development` database.

Now:

```
fly deploy --ha=false
```

(Note: the `--ha=false` --- "high availability" --- flag will prevent Fly from starting two virtual machines, thus saving you space for other projects, but making your app less snappy. You can optionally leave it off.)

Finally, we need to manually run our database table migrations on the deployed app. To do so, SSH into the app by running:

```
fly ssh console
```

Then, when you are inside the running server, run your database migrations as we have before:

```
# ruby db/migrate/*.rb
```

And that's it! When the migrations are done, your app should be deployed at `https://<your-app-name>.fly.dev` and you can manage it on your [fly.io/dashboard](https://fly.io/dashboard).

## Deploying a CRUD Rails App

Let's consider the Fly deployment case for a dynamic database-backed CRUD Rails app, like one you might build up from the [Rails template](https://learn.firstdraft.com/lessons/60#rails-7-template).

As usual, in your project's Codespace:

```
fly auth login
```

Then:

```
fly launch
```

First choose an app name, then pick a timezone.

At this point the questions you are walked through are slightly different, since we are using Rails, which is configured to work with Postgres databases by default.

Say `Y` for "yes" when asked to create a `Postgresql database`, and when you are prompted to "Select configuration", choose the first option "Development", which adheres to the "Hobby Plan" free requirement:

<!-- ![](/assets/fly-select-configuration.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686243968/fly-select-configuration_bn7rfe.png)

Next, say yes when asked to "zero after one hour":

<!-- ![](/assets/fly-zero-after-one-hour.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686244140/fly-zero-after-one-hour_m1c74a.png)

Wait a moment for the database to setup and deploy, then you will be asked to setup a `Redis database`. You can say "No".

If prompted, say "Yes" to creating a `.dockerignore` file.

When you finish, you will see `Your Ruby app is prepared for deployment`. 

Then, simply:

```
fly deploy --ha=false
```

(Note: the `--ha=false` --- "high availability" --- flag will prevent Fly from starting two virtual machines, thus saving you space for other projects, but making your app less snappy. You can optionally leave it off.)

And that's it! When the docker image finishes building and the virtual machine boots up, your app should be deployed at `https://<your-app-name>.fly.dev` and you can manage it on your [fly.io/dashboard](https://fly.io/dashboard).

## Issues and Solutions

A few issues that you might note when deploying your app with Fly:

* If the `fly deploy` command gives back an error message and fails, try to first run `fly agent stop`, and then run the `fly deploy` command again.

* Sometimes if you are re-creating an app that you just destroyed, your browser might [cache the previous DNS](https://community.fly.io/t/failed-to-proxy-http-request-on-fresh-deploy/12107/2) and prevent the app from opening when you visit it. 
  * You can try to visit the site in another browser (e.g. Safari, Mozilla). 
  * Or, depending on your browser there are different ways to clear the cache. For Chrome, type this into your URL search bar `chrome://net-internals/#dns`, and click "Clear host cache", and then type into the URL search bar `chrome://net-internals/#sockets`, and click "Flush socket pools".

* If you exit the Codespace that you deployed your Fly app from and then recreate or restart the workspace, you may get the error `Error: tunnel unavailable: failed probing "personal": context deadline exceeded` when you try to `fly ssh console` into your app. In that case, run the command `fly wireguard reset`, which should reset the connection and allow you to SSH into your VM again.

## Addendum: Continuous Deployment and Scheduled Jobs

This section covers two aspects of Fly deployment that you might find useful:

* automatic re-deploys of your Fly app anytime you publish changes to the `main` branch on the associated GitHub repository, and
* running scheduled jobs to e.g. reset `sample_data` or do another recurring task.

We cover them in the context of a Rails app (e.g. one you might create with the `appdev-projects/rails-7-template`), but the steps should be similar for a Sinatra app. Ask an instructor if you are having trouble with the steps.

### Continuous Deployment

Following the [Fly instructions](https://fly.io/docs/app-guides/continuous-deployment-with-github-actions/), you can setup a GitHub Action in your GitHub repository for the project to re-deploy the app to Fly anytime you make changes to the code and publish (push) those changes to the repo.

All you need to do is create a new directory and folder in your root folder: `.github/workflows/fly.yml`.

Make sure that you create the first `.github` folder with the `.` at the start.

In the new `fly.yml` file, simply add this content:

```yml
name: Fly Deploy
on:
  push:
    branches:
      - main
jobs:
  deploy:
    name: Deploy app
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: superfly/flyctl-actions/setup-flyctl@master
      - run: flyctl deploy --remote-only --ha=false
        env:
          FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}
```

Commit and push that change to GitHub to add the file to the published repository. (You can optionally remove the `--ha=false` flag from the `run:` step in the `fly.yml` file. Drop this flag if your app was previously deployed on multiple machines.)

Note the line `FLY_API_TOKEN: ${{ secrets.FLY_API_TOKEN }}`. 

In order to make this actually work, you will need to: 

1. Visit your app in the [fly.io/dashboard](https://fly.io/dashboard)

2. Navigate to the "Tokens" tab and create a new token called "fly deploy token" then click "Create Deploy Token". In the "Token Generated!" message, copy the token to your clipboard.

    <!-- ![](/assets/fly-deploy-token-1.png) -->
    ![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686158904/fly-deploy-token-1_tahrar.png)

    <!-- ![](/assets/fly-deploy-token-2.png) -->
    ![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686158904/fly-deploy-token-2_q6fyfq.png)

3. Navigate back to your GitHub repository associated with the app and go to "Settings" then find the "Secrets and variables" tab and click on "Actions". In this settings page, click on "New repository secret":

    <!-- ![](/assets/fly-deploy-token-3.png) -->
    ![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686158904/fly-deploy-token-3_t4rk6l.png)

4. On the "New secret" page, give the secret the name `FLY_API_TOKEN` (matching that line from the `fly.yml` file), and copy-paste the long token into the "Secret" box, then click "Add secret":

    <!-- ![](/assets/fly-deploy-token-4.png) -->
    ![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686158904/fly-deploy-token-4_cnvhwm.png)

You will see the token in your "Repository secrets".

Now, every time you push changes to the `main` branch, a GitHub Action will be run to automatically re-deploy the app on Fly.io using your token to authenticate it!

### Scheduled Jobs

If you have a `sample_data` task (or some other `rake` task) that you want to periodically run on your deployed app, then you will need to setup a `cron` job to do so using [supercronic](https://github.com/aptible/supercronic), which runs well in containers on Fly.

The below steps are somewhat specific to Rails, but you can view [this commit for setting it up in a Sinatra app deployed on Fly](https://github.com/bpurinton/sinatra-omnicalc/commit/abb180e1ec8bcb7a852670ffe1e425ea112c6eb2)

In your codebase, find the `Dockerfile` that was generated by Fly during the `fly launch` command. Open that file and find the line:

```dockerfile
COPY --from=build /rails /rails
```

_Directly below_ that line, and above the `RUN useradd ...` line, add the following lines:

```dockerfile
# cron stuff
RUN apt update
RUN apt -y upgrade
RUN apt -y install curl

# Latest releases available at https://github.com/aptible/supercronic/releases
ENV SUPERCRONIC_URL=https://github.com/aptible/supercronic/releases/download/v0.2.25/supercronic-linux-amd64 \
    SUPERCRONIC=supercronic-linux-amd64 \
    SUPERCRONIC_SHA1SUM=642f4f5a2b67f3400b5ea71ff24f18c0a7d77d49
RUN curl -fsSLO "$SUPERCRONIC_URL" \
 && echo "${SUPERCRONIC_SHA1SUM}  ${SUPERCRONIC}" | sha1sum -c - \
 && chmod +x "$SUPERCRONIC" \
 && mv "$SUPERCRONIC" "/usr/local/bin/${SUPERCRONIC}" \
 && ln -s "/usr/local/bin/${SUPERCRONIC}" /usr/local/bin/supercronic

COPY crontab crontab
```

After this, create a new file in your root folder called `crontab` (with no file extension). In the `crontab` file, you will put the following:

```
* * * * * bundle exec rake "sample_data"
```

* The `*` symbols tell the job how often to run (in this case hourly), and you can use [crontab.guru](https://crontab.guru/) to help you create new character strings for whatever frequency you need (e.g. `0 0 * * *` is every day at midnight UTC).
* Everything after the `*` symbols is the task that you want to run, which is a `sample_data` task in this case.

The final step, is to open the `fly.toml` file that was also generated during your initial `fly launch` and find the line under the `[[services]]` block:

```
[[services]]
  internal_port = 3000
  processes = ["web"]
```

And replace `web` with `app`:

```
[[services]]
  internal_port = 3000
  processes = ["app"]
```

And then add the following lines to the end of the `fly.toml` file:

```
[processes]
  # The command below is used to launch a Rails server; be sure to
  # replace with the command you're using to launch your server.
  web = "./bin/rails server"
  cron = "supercronic /rails/crontab"
```

Now, when you push the changes and publish them on GitHub, they will auto-deploy if you setup Continuous Deployment in the previous step. Otherwise, you can run `fly deploy --ha=false` again from the command-line in your app workspace.

Now the scheduled task should be running and you can check the app in your [fly.io/dashboard](https://fly.io/dashboard) and look at the server logs to be sure!
