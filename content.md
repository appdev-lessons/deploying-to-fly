# Deploying a web site to Fly.io

It's time to actually deploy your web site on the internet, so that anyone with the URL can visit it.

To do so, we will use [Fly.io](https://fly.io). Fly is great, because we can use if for not only static web sites, like our "Hello, World" and "Link in bio" projects, but also dynamic (i.e. Ruby backend) web sites that we'll build with the Sinatra and Rails frameworks.

Use your GitHub account [to sign-up](https://fly.io/app/sign-up), and let's see how we can serve our app.

If you would like a video demonstration of the process, [then have a look at this recording](https://share.descript.com/view/o4P2RSZ9SXE). But you should rely on the text here as your how-to guide anytime you want to deploy a non-database backed web site (another guide will cover the case where you have a database).

## Fly Offering and Payment

Fly's free usage tier (their ["Hobby Plan"](https://fly.io/docs/about/pricing/)), provides three 256MB RAM Virtual Machines, and 3GB of volume storage, which is enough to host your side projects. 

Note that after you sign-up, you will need to **enter credit card information [in the billing section](https://fly.io/dashboard/personal/billing)** of your dashboard (navigate to `fly.io/dashboard` and see the "Billing" tab). You **will not be charged** for making small side projects, but the credit card must be on file. And currently Fly does not collect monthly bills totaling less than $5, if you do happen to incur some small costs in your usage.

If you decide to host larger apps, and want something more powerful to serve real customers, then you can easily scale Fly up by increasing the CPUs and RAM available and pay as you go in CPU usage hours. Alternatively, you may end up going with a service like [Heroku](https://www.heroku.com/) or [hatchbox.io](https://hatchbox.io/). But, the Fly.io hobby plan should be sufficient (and free!) for now.

### Aside: How does Fly work?

Unlike GitHub Pages, which is serving the static files found in a given repository's root directory, or services like [Hatchbox](https://hatchbox.io/), which copy all of your code to a server and run the app similar to how you might in a Codespace environment, Fly uses [Docker images](https://www.docker.com/) run on "Virtual Machines".

You don't need to worry about the details, but essentially, Fly creates a "recipe" in a `Dockerfile` and some additional runtime settings in a `fly.toml` file. The recipe and runtime settings tell the three free machines that Fly provides how exactly to create a mini-computing environment to run a server.

In practice, deploying to Fly boils down to just a couple of simple commands run at the bash prompt: `fly launch` + `fly deploy`.

We have pre-installed Fly in both the Codespaces and Gitpod environment, but you can also [find easy instructions to do so locally](https://fly.io/docs/hands-on/install-flyctl/).

## Deploying your app

### Create a new organization

For every app that you deploy on fly, you will need to create a new "organization". This will allow you to have separate virtual machines for each app.

Navigate to your [Fly dashboard at fly.io/dashboard](https://fly.io/dashboard), and click on "Create Organization":

<!-- ![](/assets/fly-create-org-1.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1689107293/fly-create-org-1_m9ucwl.png)
{: .bleed-full }

On the next screen, give the organization a name. I will call it "hello-world-app". You can choose any name you like, but I suggest to name the organization after the app associated with it:

(Note: The guide is written for the "Hello, World" web site, but can be generalized to _any_ non-database backed web site; static HTML, or dynamic using Ruby on the backend. Just exchange "Hello, World" and "hello-world" for whatever app you are trying to deploy!)

<!-- ![](/assets/fly-create-org-2.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1689108287/fly-create-org-2_fo6zz8.png)

When you click "Create", you will be brought to the billing page of your new app. As noted above, you will need to enter credit card information, **but you will not be charged**. You will not be able to deploy your app until you enter credit card information.

You will need to enter this credit card information for every new organization you create. Fly will optionally save the information, so you should just need to enter the three digit security code associated with the card.

### Deploy!

When you are done creating a Fly organization and you've entered billing information, open the GitHub Codespace for your project (e.g. `<your-username>/hello-world`, or whichever repo you are trying to deploy).

Navigate to [the list of all of your Codespaces at github.com/codespaces](https://github.com/codespaces) to reopen it if you have since closed it. If you cannot find the Codespace, simply visit the repo page (e.g. `github.com/<your-username>/hello-world`) and create a new Codespace.

Once you are in the Codespace login to Fly by typing this at a bash prompt in the terminal tab, and clicking "Open" when prompted in the pop-up message:

```
fly auth login
```

You may need to sign in again with your GitHub account. Then on the fly.io page that opens, click "Continue as [your-username]". You should then be prompted to close the browser tab. Do that and return to your Codespace bash prompt.

Now, at the bash prompt, run:

```
fly launch
```

You will be taken through a series of steps to setup the app. 

First, you will be asked to choose an app name. Leave it blank to generate a random name by pressing <kbd>return</kbd>.

Next, use the arrow keys to select the name of the organization that you just created for the app, and press <kbd>return</kbd> when you have it highlighted:

<!-- ![](/assets/fly-launch-select-org.png) -->
![](https://res.cloudinary.com/dmxgp9oq2/image/upload/v1689108559/fly-launch-select-org_dljzj1.png)

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

At any time you can visit your app at [fly.io/dashboard](https://fly.io/dashboard) by navigating to the organization associated with the app ("hello-world-app" in this case). There you will find tabs to monitor the server logs (just like what you saw in the terminal when you ran `rackup`!) and more.

And that's it! Your web site is live and you can share that link with anyone.

If you want a custom domain name for your app, you will need to follow some additional steps. The basic process is [outlined in this manual from Fly](https://fly.io/docs/app-guides/custom-domains-with-fly/), and a "speed run" is shown [in this blog post](https://fly.io/blog/how-to-custom-domains-with-fly/).
