---
title: DSPG Quarto Blog Guide
author: Harun Celik
date: 05-16-2024
---

*Guide to building individual blogs for students in the DSPG Program with Quarto.*

# RStudio Version

If we want to deploy the Blog online using GitHub, then the first task requires authorizing a GitHub account through RStudio.
For this to work, the user must have Git installed on their device and already have a GitHub account.

## Establishing Git Connection
### Using `gitcreds`

We can use an R library called `gitcreds` to authenticate our account. The following code chunk will install the package, load the library, 
and run the `gitcreds_set()` function to initialize the authentication. This works best if you are already signed in to your account on a browser
before you run this code.

Run this code in your console.
```
install.packages("gitcreds")
library(gitcreds)
gitcreds_set()
```
You should be taken to a login page where you can enter your GitHub username and password and voila! You are connected!

### Troubleshooting General Git Connections to RStudio
Despite having meeting these requirements and being at the latest version of R and RStudio, there may still be an issue with RStudio 
finding Git.

- In RStudio navigate to `Tools > Global Options > Git/SVN` and manually set the path of the Git executable located on your device.
    - You can locate the Git executable path by running `which git` (on Mac, Linux, Git Bash shell on Windows)

If problems persist;

- Check whether Git works independently of RStudio.
- Check whether PATH on your device is set up correctly, this may require reinstalling Git.

### Troubleshooting Authorization Tokens

HTTPS protocols use something GitHub calls a `personal access token` which may need to be generated and used as a way to verify your
Git activity.

Options are to either...

1. Go to https://github.com/settings/tokens and click “Generate token”.
2. Use the `usethis` package in R to generate a token with `usethis::create_github_token()` and `gitcreds::gitcreds_set()` to add the credential.

If you are running into any more trouble, check out the [Happy Git and GitHub for the useR](https://happygitwithr.com/) book

## Generating Quarto Blog

Once the GitHub connection is established, generating the blog is actually not too difficult. We can follow the 
[Quarto Creating a Website Guide](https://quarto.org/docs/websites/#overview) for RStudio to get through the initial setup.

Some things for our use case to consider.

- Select the Quarto Blog project type. 
- Select the "create a git repository" option as we make the quarto blog.
- Your repository name will be part of the URL so make it something descriptive like `Name-DSPG24-Blog`

## Structure of Quarto Blogs

### Posts Directory
By default, all blog posts are contained in the `posts` directory. The best practice is to create a directory for each of the blog articles containing
a quarto markdown file (`.qmd`). These files are called `index.qmd`, but they can be called anything as long as they are a quarto markdown file and have the 
appropriate metadata.

### YAML Metadata
All the markdown files have a data serialization section at the top of the document referred to as the YAML. This is the way to add metadata to your markdown files
so that they generate with the relevant information.

Here is an example of a YAML containing blog title, author, date, and categorical tag information.

```
---
title: "Welcome To My Blog"
author: "Tristan O'Malley"
date: "2024-05-12"
categories: [news]
---
```

### YAML Configuration
The generated blog pages have the structure that they do because of the YAML configuration expressed in the `_quarto.yml` file in the home directory. 

Here is an example of the default one. The comments describe what each setting is for.

```
---
project:
  type: website ## Determines page layout type as website ##

website:
  title: "test" ## Website title on the top left ##
  navbar:
    right: ## Indicates what to populate the right side of navbar ##
      - about.qmd
      - icon: github
        href: https://github.com/
      - icon: twitter
        href: https://twitter.com
format:
  html:
    theme: cosmo
    css: styles.css ## indicates a style sheet ##
---
```
There are too many options to discuss all the possibilities of customizing the Quarto blogs, so the best thing would be to check out the
[documentation page on websites](https://quarto.org/docs/websites/#overview) as reference to see what is possible. 

## Rendering Quarto Blogs
The basic premise of the markdown files is that you write whatever you need to in Markdown, *you then render the material*, and then you have
all the webpage related material all made for you.

There are a couple of different ways to render your pages.

1. If any `.qmd` file is open, there is an arrow on the top of the code pane that says `Render`. Clicking it or hitting `Ctrl+Shift+K` will render the page.
2. Using the quarto package, run the function `quarto::quarto_render()`.

Both of these options will display a live, but locally hosted, version of your quarto project which will react to changes you make in the Markdown document.

#### Exploration: Adjust Your About Pages
Now that you know how things are structured, go ahead and try to arrange the About page of your websites.

# Deploying Blog to GitHub Pages 
GitHub Pages offers a free platform to host your blog live for public viewing. There are a couple of adjustments we need to make to the YAML configuration 
in order for this to work smoothly.

## Changing Output Directory
Since GitHub pages deploys from a `docs\` directory, we will need to tell Quarto to render our pages to a directory called `docs`. This is done by adding an 
`output-dir: docs` argument under project.

Here is an example. 
```
---
project:
  type: website
  output-dir: docs

website:
  title: "DSPG 2024"
  navbar:
    right:
      - icon: github
        href: https://github.com/DSPG-2024
    left: 
      - href: index.qmd
        text: Home
---
```

You must render by running `quarto::quarto_render()` in the console for this to take effect. If successful, there will now be a `docs` folder in your quarto directory.

## A nojekyll File
Jekyll is a site generator that GitHub has integrations with and if we don't indicate that we don't want our page to render with jekyll it will conflict with the web material
generated by quarto. By adding a file in the repository with the title `.nojekyll`, GitHub knows not to run jekyll for us.

## GitHub `docs\` Setting
Now it's time to deploy the blog.

In your repository, go to and configure the following `Settings > Pages > Source > Deploy from a branch > main > docs`. Save your settings and give GitHub a minute to deploy the
page. After refreshing the page you should see a message above that reads `Your site is live at _URL_`. Visit the site and enjoy!

# Important Notes
## Data Privacy
With the way we've set up the blogs in this document, all the data that is used to generate your blogs is public. Do not put anything into these directories that you don't trust
insecurely on the web.

## General Workflow of Maintaining Blogs
Whenever you want to make a change to your blogs follow the general guideline below.

1. Go to your blog R project and make the changes you want.
2. Render the page with `quarto::quarto_render()`.
3. Push all of your changes to the GitHub repository.
4. Wait for the GitHub repository to deploy the page and visit once finished.