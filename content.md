# Deploying a web site to Fly.io

It's time to actually deploy your web site on the internet, so that anyone with the URL can visit it.

To do so, we will use [Fly.io](https://fly.io). Fly is great, because we can use if for not only static web sites, like our "Hello, World" and "Link in bio" projects, but also dynamic (i.e. Ruby backend) web sites that we'll build with the Sinatra and Rails frameworks.

Use your GitHub account [to sign-up](https://fly.io/app/sign-up), and let's see how we can serve our app.

<div class="bg-red-100 py-1 px-5" markdown="1">

You are welcome to try out Fly, which has a very easy deployment process. However, some students have reported charges for deploying small apps. To avoid potential credit card charges, we recommend using Render to deploy, as that does not require a credit card to sign up. [See our guide on Render for those steps](https://learn.firstdraft.com/lessons/114-deploying-to-render).
</div>

## Fly Offering and Payment

Fly's free usage tier (their ["Hobby Plan"](https://fly.io/docs/about/pricing/)), provides three 256MB RAM Virtual Machines, and 3GB of volume storage, which is enough to host your side projects. 

<div class="bg-blue-100 py-1 px-5" markdown="1">

After you sign-up, you will need to **enter credit card information [in the billing section](https://fly.io/dashboard/personal/billing)** of your dashboard (navigate to `fly.io/dashboard` and see the "Billing" tab). You may see a small (less than $1) initial charge on your card, but that is just for verification and will be cancelled soon. Fly waives bill collection for monthly account usage below $5.
</div>

If you decide to host larger apps, and want something more powerful to serve real customers, then you can easily scale Fly up by increasing the CPUs and RAM available and pay as you go in CPU usage hours. Alternatively, you may end up going with a service like [Heroku](https://www.heroku.com/) or [hatchbox.io](https://hatchbox.io/). But, the Fly.io hobby plan should be sufficient (and free!) for now.

### Aside: How does Fly work?

Unlike GitHub Pages, which is serving the static files found in a given repository's root directory, or services like [Hatchbox](https://hatchbox.io/), which copy all of your code to a server and run the app similar to how you might in a codespace environment, Fly uses [Docker images](https://www.docker.com/) run on "Virtual Machines".

You don't need to worry about the details, but essentially, Fly creates a "recipe" in a `Dockerfile` and some additional runtime settings in a `fly.toml` file. The recipe and runtime settings tell the three free machines that Fly provides how exactly to create a mini-computing environment to run a server.

In practice, deploying to Fly boils down to just a couple of simple commands run at the bash prompt: `fly launch` + `fly deploy`.

We have pre-installed Fly in both the Codespaces and Gitpod environment, but you can also [find easy instructions to do so locally](https://fly.io/docs/hands-on/install-flyctl/).

## Deploying your app

When you are done creating a Fly account and you've entered billing information, open the GitHub Codespace for your project (e.g. `<your-username>/hello-world`, or whichever repo you are trying to deploy).

Navigate to [the list of all of your codespaces at github.com/codespaces](https://github.com/codespaces) to reopen it if you have since closed it. If you cannot find the codespace, simply visit the repo page (e.g. `github.com/<your-username>/hello-world`) and create a new codespace.

Once you are in the codespace, make sure your Fly version is up-to-date by running this at the bash prompt in the terminal tab:

```
fly version upgrade
```

Once you've updated, login to Fly by typing this at a bash prompt, and clicking "Open" when prompted in the pop-up message:

```
fly auth login
```

You may need to sign in again with your GitHub account. Then on the fly.io page that opens, click "Continue as [your-username]". You should then be prompted to close the browser tab. Do that and return to your codespace bash prompt.

Now, at the bash prompt, run:

```
fly launch
```

You will be taken through a series of steps to setup the app. 

First, you will be asked to choose an app name. Leave it blank to generate a random name by pressing <kbd>return</kbd>.

Next, use the arrow keys to select the appropriate timezone (you can also begin typing a city/state/country name and it will filter the list) and press <kbd>return</kbd> to select it:

<!-- ![](/assets/fly-3-timezone.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686111337/fly-3-timezone_fvbwnt.png)

For the next question about a `Postgresql database`, type `N` for "no", since we are just deploying a database-free app for now. 

You will also be prompted to setup a `Redis database`. Say `N` for "no".

If you are asked to create a `.dockerignore` file, say `Y` for "yes".

<!-- ![](/assets/fly-4-database.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1686111337/fly-4-database_bjyjdp.png)

When you finish, you will see `Your Ruby app is prepared for deployment`, and you will be returned to the bash prompt.

The `fly launch` command created some new files in your repository: `.dockerignore` (only if a `.gitignore` was already present), `Dockerfile`, and `fly.toml`. These files are the instructions that Fly will follow to create and serve your app on your virtual machines hosted by Fly.io.

This is a great time to make a [git commit with the message "fly launch success"](https://learn.firstdraft.com/lessons/50-git-commit-and-push#committing-changes). By committing (and eventually pushing with the "Sync" button), you will publish the Fly recipe in the repo on GitHub, so that you can make changes to it later if needed.

There is just one more command to run at the bash prompt to deploy the app:

```
fly deploy
```

That command will take a minute or two and there will be a lot of output at your terminal. Fly is building a docker image and pushing it to the server, and then the server is launching. Wait patiently until you are returned to your empty bash prompt.

When it is done, you can launch into the deployed app by visiting the URL `https://<your-app-name>.fly.dev`, or by simply entering at the bash prompt:

```
fly open
```

The app will have a randomly generated name, if you left the name blank in the first `fly launch` step. In my case, it happens to be `https://crimson-voice-4018.fly.dev`.

At any time you can visit your app at [fly.io/dashboard](https://fly.io/dashboard). There you will find tabs to monitor the server logs (just like what you saw in the terminal when you ran `rackup`!) and more.

And that's it! Your web site is live and you can share that link with anyone.

<div class="bg-blue-100 py-1 px-5" markdown="1">

Have you made changes to your app in the codespace that you would like to re-deploy? It's as easy as re-running the `fly deploy` command once form your codespace after you make the changes. Because the "recipe" is saved in your `fly.toml` and `Dockerfile`, Fly will handle everything and the changes you made to your app will show up when you re-run this command. Just be sure that you commit and push your changes to GitHub for safekeeping and the ability to go back in time to previous versions of your app.
</div>

If you want a custom domain name for your app, you will need to follow some additional steps. The basic process is [outlined in this manual from Fly](https://fly.io/docs/app-guides/custom-domains-with-fly/), and a "speed run" is shown [in this blog post](https://fly.io/blog/how-to-custom-domains-with-fly/).

---

<div class="bg-blue-100 py-1 px-5" markdown="1">

Are you seeking to deploy an app that has a database? Read the next section for the additional steps to take there.

You don't need to read that section until we get to Ruby on Rails and databases. The deployment steps are almost identical, with just one difference!
</div>

## Deploying a database-backed Rails app

Proceed as in the previous steps by launching the codespace, updating the fly version and running the `fly launch` command.

The difference to the previous step comes when selecting a `Postgresql database`. This time you will say `Y` for "yes". Then, be sure to select the `Development` configuration:

![](/assets/select-development-db.png)

And `y` for "yes" to spin down the database after an hour of inactivity:

![](/assets/zero-after-one-hour.png)

This step will take a moment while the database is created. When it is done, you can proceed as in the previous steps by saying:

- `N` for "no" to a `Redis database`
- `Y` for "yes" to a `.dockerignore` file

When you finish, you will see `Your Ruby app is prepared for deployment`, and you will be returned to the bash prompt.

The `fly launch` command created some new files in your repository: `.dockerignore` (only if a `.gitignore` was already present), `Dockerfile`, and `fly.toml`. These files are the instructions that Fly will follow to create and serve your app on your virtual machines hosted by Fly.io.

This is a great time to make a [git commit with the message "fly launch success"](https://learn.firstdraft.com/lessons/50-git-commit-and-push#committing-changes). By committing (and eventually pushing with the "Sync" button), you will publish the Fly recipe in the repo on GitHub, so that you can make changes to it later if needed.

There is just one more command to run at the bash prompt to deploy the app:

```
fly deploy
```

That command will take a minute or two and there will be a lot of output at your terminal. Fly is building a docker image and pushing it to the server, and then the server is launching. Wait patiently until you are returned to your empty bash prompt.

When it is done, you can launch into the deployed app by visiting the URL `https://<your-app-name>.fly.dev`, or by simply entering at the bash prompt:

```
fly open
```

At any time you can visit your app at [fly.io/dashboard](https://fly.io/dashboard). There you will find tabs to monitor the server logs and more.

And that's it! Your web site is live and you can share that link with anyone.

<div class="bg-blue-100 py-1 px-5" markdown="1">

As before, just re-run the `fly deploy` command to migrate any changes you make to your app to the live deployment.
</div>


If you want a custom domain name for your app, you will need to follow some additional steps. The basic process is [outlined in this manual from Fly](https://fly.io/docs/app-guides/custom-domains-with-fly/), and a "speed run" is shown [in this blog post](https://fly.io/blog/how-to-custom-domains-with-fly/).

---
