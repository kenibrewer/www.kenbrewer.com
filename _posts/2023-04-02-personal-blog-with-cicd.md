---
layout: post
title: Setting up a personal blog with CI/CD
subtitle: Why and how I set up a personal blog using GitHub Pages, Jekyll, and GitHub Actions
gh-repo: kenibrewer/www.kenbrewer.com
gh-badge: [follow]
tags: [devops,biotech,bioinformatics,github,github-pages,jekyll,ci/cd,github-actions]
comments: true
---

Based on the recommendation of *multiple* colleagues in the Flagship Pioneering informatics community, I recently listed to [The Phoenix Project](https://itrevolution.com/product/the-phoenix-project/) on audiobook. 
This fictional novel focuses on a struggling IT department within a company, and how the main character, Bill, turns things around by implementing principles that are now core to the DevOps movement.
It is an engaging and educational read, and I highly recommend it to anyone interested in DevOps.


Although Bill's story is set in a medium-sized manufacturing company, I found many of the challenges he faced relatable as a computational biologist working in a small biotech startup. 
His frantic efforts to ensure integrity of critical data for his colleagues in HR and finance reminded me of the urgency I feel when delivering bioinformatic analyses to bench scientists who need them for their experiments.


The novel inspired me to consider how I could apply DevOps principles to the specific problems I face at work. 
I was somewhat surprised, when I realized that I already had most of the necessary technical skills, but lacked a framework to apply them in an integrated way.
That led to my decision to set up a blog focused on applying the principles of DevOps to specific problems faced by computational biologists, bioinformaticians, machine learning engineers and data scientists who work in the biotech and pharmaceutical industries.

## Criteria for the blog

If I wanted to write a blog that would in part be focused on DevOps, I wanted to set up the website using that same set of principles I wanted to write about.
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




