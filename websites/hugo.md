# Hugo

## New site
Get Hugo to generate a new site:
```
hugo new site blog
```

Let's check it out!
```
cd blog
ls -1

archetypes
config.toml
content
data
layouts
static
themes
```

## Git
Initialize git repo
```
git init
```

## Theme
Let's install a theme. We will add this as a Git `submodule` (so that its history will be kept separate from our own code)
```
git submodule add https://github.com/budparr/gohugo-theme-ananke.git themes/ananke;
```

Now we have added that repo inside our `themes` directory - `/themes/ananke`

Git has also created a `.gitmodules` file to keep track of what's going on:
```
[submodule "themes/ananke"]
	path = themes/ananke
	url = https://github.com/budparr/gohugo-theme-ananke.git
```


## Config
We need to let Hugo know that we want to use that theme.

In `.config.toml`, add this line:
```
theme = "ananke"
```

We also need to let Hugo how to construct the internal links properly - we need
to provide a `baseURL` - we will be using GitHub Pages, so we need to add our
repository name as subdirectory.

```
baseURL = "https://danielmoi.github.io/blog"
```

## Content
Let's make our first page!
```
hugo new post/hello.md
```

Edit `/content/post/hello.md` and remove `draft: true` so that it will be generated later on.


## Deploy locally
Start our server in watch mode, and include drafts
```
hugo server -w -D
```

Check out our new site in the browser at the address given!


```
http://localhost:1313/blog
```

Exciting!

## Generate static giles
Note that we don't have a `public` folder. Our site is being deployed locally,
but we haven't generated the static files yet. Let's do that.
```
hugo
```

Ridiculously simple.

Hugo would have told us that it did:
```
@ hugo
Started building sites ...

Built site for language en:
0 draft content
0 future content
0 expired content
1 regular pages created
8 other pages created
0 non-page files copied
1 paginator pages created
0 tags created
0 categories created
total in 7 ms
```

## Gitignore
We don't want to commit our generated static files into version control. We need
them to generate our site, but we don't need to add them to our `master` branch.

Create a `.gitignore` and add `public`:
```
public
```

## First commit
Let's commit what we've done so far.

```
No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   .gitmodules
	new file:   themes/ananke

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	.gitignore
	archetypes/
	config.toml
	content/

```

```
git add .
git commit -m 'First commit'
```

Let's add a remote, set an upstream, and push up to GitHub
```
$ git remote add origin git@github.com:danielmoi/blog.git
$ git push -u origin master
```

## Setup gh-pages branch
We will be using this `gh-pages` branch to deploy to GitHub Pages.
We don't need any of the previous commits, so let's start afresh.
We do this by setting this up as an ORPHAN branch.

```
git checkout --orphan gh-pages
```

Let's see what's in there
```
$ git status

On branch gh-pages

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   .gitignore
	new file:   .gitmodules
	new file:   archetypes/default.md
	new file:   config.toml
	new file:   content/post/hello.md
	new file:   themes/ananke
```

Now let's reset the branch.

```
git reset --hard
```

This will remove most of the files, but we need to remove `public` and `themes`

We can check what's left:
```
public
themes
```

That's okay, because we will not add anything, we are just going to commit this as an (empty) branch.


```
git commit --allow-empty -m "Initializing gh-pages branch"
```

Now let's push to GitHub

```
git push origin gh-pages
```

If we check out this branch on GitHub, we can confirm that there is a commit,
but are no files or directories in it.

This is now a new branch, with NOTHING in it.

## Setup public folder to hold that gh-pages branch


We will use git worktrees to accomplish this.

We start off in the root directory, and on master branch.
```
$ git checkout master
```

First, we need to remove our `public` directory
```
rm -rf public
```

Now, let's set a worktree.
When we `cd` into `public`, we want to be checking out the `gh-pages` branch.
That is, in our `root` directory, we will have our `master` (and other)
branches, but in our `public` directory, we will be in our `gh-pages` branch.

```
git worktree add -B gh-pages public origin/gh-pages
```


```
-B                Create / Reset branch "gh-pages"

public            This is the path (directory) we want our worktree to live in

origin/gh-pages   This is the branch in the cloud
```

Git should have told us what it did:
```
Branch 'gh-pages' set up to track remote branch 'gh-pages' from 'origin'.
Preparing public (identifier public)
HEAD is now at 445ffe6 Initialize gh-pages branch
```

Note that we need Git 2.5 or higher for this.

On MacOS, check the version of Git:
```
$ git --version
```

And to upgrade Git,
```
brew upgrade git
```


## Check that worktree is working
Note that we if try to `checkout` our `gh-pages` branch, we will get an error:

```
$ gco gh-pages
fatal: 'gh-pages' is already checked out at '<path-to-repo>/blog/public'
```

However, if we `cd` into `public`, we can see that we have `checkout`ed our
`gh-pages` branch! Don't worry if this is confusing - it will make more sense
moving forward.

Likewise, if we try to `checkout` our `master` branch, we get a similar error:
```
$ gco master
fatal: 'master' is already checked out at '<path-to-repo>/blog'
```

We have TWO branches checked out simultaneously!



## Populate our gh-pages branch with out static files
Let's go back to our master branch.
This means going UP ONE DIRECTORY, instead of changing branch, per se.
```
$ cd ..
```

Now let's get Hugo to generate our static files again.
```
hugo
```
Again, Hugo tells us what it has done:
```
@ hugo
Started building sites ...

Built site for language en:
0 draft content
0 future content
0 expired content
1 regular pages created
8 other pages created
0 non-page files copied
0 paginator pages created
0 tags created
0 categories created
total in 2 ms

```

Now, when we `cd` into `public` we should see (ONLY) our static files - our new
site!

Let's check our Git status:
```
On branch gh-pages
Your branch is up to date with 'origin/gh-pages'.

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	404.html
	categories/
	dist/
	index.html
	index.xml
	post/
	sitemap.xml
	tags/

```

Perfect.

Let's add all, commit, and push.

```
$ git commit -m 'Thanks Hugo'
[gh-pages 2e4cbfb] Thanks Hugo
 14 files changed, 951 insertions(+)
 create mode 100644 404.html
 create mode 100644 categories/index.html
 create mode 100644 categories/index.xml
 create mode 100644 dist/app.bundle.js
 create mode 100644 dist/main.css
 create mode 100644 images/gohugo-default-sample-hero-image.jpg
 create mode 100644 index.html
 create mode 100644 index.xml
 create mode 100644 post/hello/index.html
 create mode 100644 post/index.html
 create mode 100644 post/index.xml
 create mode 100644 post/page/1/index.html
 create mode 100644 sitemap.xml
 create mode 100644 tags/index.html
 create mode 100644 tags/index.xml
 ```

## Check out the live site
Note that you might need to go to GitHub / Settings
And make `gh-pages` branch the Source for GitHub pages.


