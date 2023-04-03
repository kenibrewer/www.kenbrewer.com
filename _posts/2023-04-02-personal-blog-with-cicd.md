---
layout: post
title: Setting up a personal blog with CI/CD
subtitle: Why and how I set up a personal blog using GitHub Pages, Jekyll, and GitHub Actions
gh-repo: kenibrewer/www.kenbrewer.com
gh-badge: [star, fork, follow]
cover-img: ['assets/img/coffee_and_coding.jpg']
tags: [devops,biotech,github,github-pages,jekyll,ci/cd,github-actions]
readtime: true
comments: true
---

Based on the recommendation of *multiple* colleagues in the Flagship Pioneering informatics community, I recently listened to [The Phoenix Project](https://itrevolution.com/product/the-phoenix-project/) on audiobook. 
This novel focuses on a struggling IT department within the fictional company Parts Unlimited, and how the main character, Bill, turns things around by implementing the core principles of the DevOps movement.
It is an engaging and educational read, and I highly recommend it to anyone interested in DevOps.


Although Bill's story is set in a medium-sized manufacturing company, I found many of the challenges he faced relatable as a computational biologist working in a small biotech startup. 
His frantic efforts to ensure integrity of critical data for his colleagues in HR and finance reminded me of the urgency I feel when delivering bioinformatic analyses to bench scientists who need them for their experiments.


The novel inspired me to consider how I could apply DevOps principles to the specific problems I face at work. 
As I started diving deeper into DevOps, I was somewhat surprised to discover that I already possessed all the core technical skills. 
I had simply lacked a framework to apply those technical skills in an integrated, synergistic way.
That led to my decision to set up a blog focused on applying the principles of DevOps to specific problems faced by computational biologists, bioinformaticians, machine learning engineers and data scientists who work in the biotech and pharmaceutical industries.

## Criteria for the blog

If I wanted to write a blog that would in part be focused on DevOps, I wanted to set up the website using that same set of principles I wanted to write about.
The DevOps principles I wanted to apply to the blog were:

* Automate everything
* Use version control
* Use a CI/CD pipeline to deploy the site

I also had a few other criteria for the blog:

* Use a static site generator to make the blog easy to maintain.
* Be able to write new posts in markdown.
* Keep costs as low as possible, preferably free.
* Be able to use a custom domain name.
* Have sufficient flexibility to customize the look and feel of the site.

## Static site generator

I decided to use [Jekyll](https://jekyllrb.com/) as the static site generator for the blog.
Jekyll is a popular choice for static site generators, and is well supported by GitHub Pages.
Because this was my first time using Jekyll and I don't believe in re-inventing the wheel, I decided to use a pre-built theme to get started.
I chose [Beautiful Jekyll](https://beautifuljekyll.com/) because it is well documented, and has a clean, modern look.

## Setting up the blog using CI/CD

I largely followed the instructions on the Beautiful Jekyll website to set up the blog, but I made a few modifications in-line with this blog's focus on DevOps and automation.

### Setting up a DevContainer

Earlier this year, I led an effort at ProFound to set up standardized development environments.
I'll write more about that effort in a future post, but I wanted to use the same approach for this blog.

VsCode makes it trivially easy to [set up a DevContainer](https://code.visualstudio.com/docs/devcontainers/create-dev-container#_automate-dev-container-creation) for a project even for a project that uses a framework/language that you are not familiar with.
Using the "Add Development Container Configuration Files" command, I was able to quickly generate a `devcontainer.json` file that would launch a Docker container with all the necessary dependencies for Beautiful Jekyll.

```json
{
	"name": "Jekyll",
	"image": "mcr.microsoft.com/devcontainers/jekyll:0-buster",
	"features": {
		"ghcr.io/devcontainers/features/node:1": {}
	},
	"forwardPorts": [4000],
	"postCreateCommand": "bundle exec jekyll serve --watch"
}
```

As soon as this file was saved, VSCode automatically prompted me to re-open the project in the container.

### Setting up a Cloud IDE

One of the benefits of using a DevContainer is that you can use the same container to develop locally or in the cloud using [Github Codespaces](https://github.com/features/codespaces). 
Github Codespaces has a generous free tier, and is a great way to reduce the friction of setting up a new project.
You can read more about how to set up a Github Codespace in the [Github documentation](https://docs.github.com/en/codespaces/developing-in-codespaces/creating-a-codespace).

## Setting up a CI/CD pipeline

The original repo for Beautiful Jekyll included a simple [GitHub Actions workflow](https://github.com/daattali/beautiful-jekyll/blob/e1facea35a0a8ee81bc204db10039d5b53837a39/.github/workflows/ci.yml).
While enabling the Github Pages feature in the repo settings, I found a template Github Actions pipeline that can be used to build and deploy the site instead:

```yaml
# Sample workflow for building and deploying a Jekyll site to GitHub Pages
name: Deploy Jekyll site to Pages

on:
  push:
    branches: ["main"]
  workflow_dispatch:

# Sets permissions of the GITHUB_TOKEN to allow deployment to GitHub Pages
permissions:
  contents: read
  pages: write
  id-token: write

# Allow only one concurrent deployment, skipping runs queued between the run in-progress and latest queued.
# However, do NOT cancel in-progress runs as we want to allow these production deployments to complete.
concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  # Build job
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Setup Ruby
        uses: ruby/setup-ruby@ee2113536afb7f793eed4ce60e8d3b26db912da4 # v1.127.0
        with:
          ruby-version: '3.1' # Not needed with a .ruby-version file
          bundler-cache: true # runs 'bundle install' and caches installed gems automatically
          cache-version: 0 # Increment this number if you need to re-download cached gems
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v3
      - name: Build with Jekyll
        # Outputs to the './_site' directory by default
        run: bundle exec jekyll build --baseurl "${{ steps.pages.outputs.base_path }}"
        env:
          JEKYLL_ENV: production
      - name: Upload artifact
        # Automatically uploads an artifact from the './_site' directory by default
        uses: actions/upload-pages-artifact@v1

  # Deployment job
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

## Setting up a custom domain

I wanted to use a custom domain name for the blog, so I followed the instructions on the [Github Pages documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site) to set up a custom domain.
In order to use a custom domain, you need to create a CNAME record in your DNS settings that points to `<username>.github.io`.
I did this manually in the console for Cloudflare, but I plan to integrate these settings into a Terraform configuration file that I'll build into this repo in the future.
I could have waited until I had the Terraform setup ready to go too, but getting a minimum viable product up and running quickly is a key part of the DevOps philosophy.

## Conclusion

I'm really happy with how with how simple it was to set up this blog using DevOps principles.
It only took a few hours to set up the blog, and I have something simple and robust enough for me to write posts and iterate on with little-to-no overhead.
I'm looking forward to writing more posts in the future, and I hope you'll join me on this journey!

## References

* [Beautiful Jekyll](https://beautifuljekyll.com/)
* [Jekyll](https://jekyllrb.com/)
* [Github Pages](https://pages.github.com/)
* [Github Actions](
https://docs.github.com/en/actions)

#### Image credits

Cover image credit by [Nathan da Silva](https://unsplash.com/@silvawebdesigns) on [Unsplash](https://unsplash.com/photos/k-rKfqSm4L4)
  
