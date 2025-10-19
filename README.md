<<<<<<< HEAD
<html>
<head>
<title>Ferosh Jacob, Ph.D.</title>
<meta http-equiv="Content-Type" content="text/html;charset=iso-88591"/>
  <LINK href="style.css" rel="stylesheet" type="text/css"/>  
</head>
 
<body>
<div id="header"> 

<div id="logo">
<img src="images/logo.gif"/>
</div>
<div id="title">
	Ferosh Jacob, Ph.D.<br/>
	feroshjacob(@)gmail.com
</div>
<div id="menu">
<table>
    <tr>
      <td><strong>Home </strong></td>
      <td><a href="ms.html">M.S. Thesis</a></td>
      <td><a href="phd.html">Ph.D. Dissertation</a></td>
      <td><a href="research.html">Presentations</a></td>
      <td><a href="publications.html">Publications</a></td>
    </tr>
  </table>
</div>
</div>
<div id="main-container"> 
    <p class="centerimage"> <strong>AREAS OF INTEREST</strong><br/><br/>
	My primary research area of interest lies in software engineering. I have worked in many interdisciplinary projects involving software engineering and 
	several fields (e.g., High-Performance computing, Machine learning, Data mining, Mathematics, Cloud computing) to improve design, development, and maintenance of software.
	I carefully 1) analyzed existing tool support 2) proposed new solutions, and 3) determined advantages and disadvantages of the proposed approach compared to the existing 
	tools in the relevant area. 


<br/>
<br/>

My Ph.D dissertation is based on applying software modeling techniques to computation intensive problems for heterogeneous computing and improved source code maintenance. 
	I was supported by a grant from Air Force/AFRL STTR, which I helped Dr. Jeff Gray in preparing and submitting. Much of the work for this grant is part of my Ph.D. thesis.
Based on our performance in Phase I, we were invited to submit proposal for Phase II. I completed my Master's thesis on providing tool support for software code clones.

</p>
<div id="leftcolumn2"  >
    <p class="centerimage"> <strong>EDUCATION</strong><br/><br/>


Ph.D., Department of Computer Science, The University of Alabama,Tuscaloosa, AL 2013<br/>
M.S., Department of Computer Science, Clarkson University, Potsdam, NY 2009<br/>
B.Tech., Electronics and Communication Engineering, NIT, Calicut, India 2004<br/>
</p>
<p class="centerimage">  <br/><br/>
<a href= "files/resume.pdf">Curriculum Vitae</a>
<a href="https://scholar.google.com/citations?user=BHni1Y8AAAAJ&hl=en"><img height="50px" src="images/Google_Scholar_logo_2015.png" alt="Google Scholar"></a>
<a href="https://www.linkedin.com/in/ferosh-jacob-03b8109"><img height="50px" src="images/LinkedIn-Logo-2C.png" alt="Linkedin"></a>
<a href="http://dblp.uni-trier.de/pers/hd/j/Jacob:Ferosh" ><img height="50px" src="images/dblp-5.png" alt="DBLP"></a>
<a href="http://dl.acm.org/author_page.cfm?id=81456613588&coll=DL&dl=ACM&trk=0&cfid=724861222&cftoken=44157594" ><img height="50px" src="images/ACMDL_Logo_Alt_Color.png" alt="ACM Digital Library"></a>
<a href=" https://www.researchgate.net/profile/Ferosh_Jacob " ><img height="50px" src="images/ResearchGate_Logo.png " alt="Research Gate"></a>
</p>
</div>

</div>

</body>
</html>


=======
# Academic Pages
**Academic Pages is a GitHub Pages template for personal and professional portfolio-oriented websites.**

![Academic Pages template example](images/themes/homepage-light.png "Academic Pages template example")

# Getting Started

1. Register a GitHub account if you don't have one and confirm your e-mail (required!)
1. Click the "Use this template" button in the top right.
1. On the "New repository" page, enter your public repository name as "[your GitHub username].github.io", which will also be your website's URL.
1. Set site-wide configuration and add your content.
1. Upload any files (like PDFs, .zip files, etc.) to the `files/` directory. They will appear at https://[your GitHub username].github.io/files/example.pdf.
1. Check status by going to the repository settings, in the "GitHub pages" section
1. (Optional) Use the Jupyter notebooks or python scripts in the `markdown_generator` folder to generate markdown files for publications and talks from a TSV file.

See more info at https://academicpages.github.io/

## Running locally

When you are initially working on your website, it is very useful to be able to preview the changes locally before pushing them to GitHub. To work locally you will need to:

1. Clone the repository and made updates as detailed above.

### Using a different IDE
1. Make sure you have ruby-dev, bundler, and nodejs installed
    
    On most Linux distribution and [Windows Subsystem Linux](https://learn.microsoft.com/en-us/windows/wsl/about) the command is:
    ```bash
    sudo apt install ruby-dev ruby-bundler nodejs
    ```
    If you see error `Unable to locate package ruby-bundler`, `Unable to locate package nodejs `, run the following:
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```
    then try run `sudo apt install ruby-dev ruby-bundler nodejs` again.

    On MacOS the commands are:
    ```bash
    brew install ruby
    brew install node
    gem install bundler
    ```
1. Run `bundle install` to install ruby dependencies. If you get errors, delete Gemfile.lock and try again.

    If you see file permission error like `Fetching bundler-2.6.3.gem ERROR:  While executing gem (Gem::FilePermissionError) You don't have write permissions for the /var/lib/gems/3.2.0 directory.` or `Bundler::PermissionError: There was an error while trying to write to /usr/local/bin.`
    Install Gems Locally (Recommended):
    ```bash
    bundle config set --local path 'vendor/bundle'
    ```
    then try run `bundle install` again. If succeeded, you should see a folder called `vendor` and `.bundle`.

1. Run `jekyll serve -l -H localhost` to generate the HTML and serve it from `localhost:4000` the local server will automatically rebuild and refresh the pages on change to Markdown (*.md) and HTML files, while changes to the core template and configuration (i.e., `_config.yml`) will require stoping and restarting Jekyll.
    You may also try `bundle exec jekyll serve -l -H localhost` to ensure jekyll to use specific dependencies on your own local machine.

If you are running on Linux it may be necessary to install some additional dependencies prior to being able to run locally: `sudo apt install build-essential gcc make`

## Using Docker

Working from a different OS, or just want to avoid installing dependencies? You can use the provided `Dockerfile` to build a container that will run the site for you if you have [Docker](https://www.docker.com/) installed.

You can build and execute the container by running the following command in the repository:

```bash
chmod -R 777 .
docker compose up
```

You should now be able to access the website from `localhost:4000`.

### Using the DevContainer in VS Code

If you are using [Visual Studio Code](https://code.visualstudio.com/) you can use the [Dev Container](https://code.visualstudio.com/docs/devcontainers/containers) that comes with this Repository. Normally VS Code detects that a development coontainer configuration is available and asks you if you want to use the container. If this doesn't happen you can manually start the container by **F1->DevContainer: Reopen in Container**. This restarts your VS Code in the container and automatically hosts your academic page locally on http://localhost:4000. All changes will be updated live to that page after a few seconds.

# Maintenance

Bug reports and feature requests to the template should be [submitted via GitHub](https://github.com/academicpages/academicpages.github.io/issues/new/choose). For questions concerning how to style the template, please feel free to start a [new discussion on GitHub](https://github.com/academicpages/academicpages.github.io/discussions).

This repository was forked (then detached) by [Stuart Geiger](https://github.com/staeiou) from the [Minimal Mistakes Jekyll Theme](https://mmistakes.github.io/minimal-mistakes/), which is © 2016 Michael Rose and released under the MIT License (see LICENSE.md). It is currently being maintained by [Robert Zupko](https://github.com/rjzupkoii) and additional maintainers would be welcomed.

## Bugfixes and enhancements

If you have bugfixes and enhancements that you would like to submit as a pull request, you will need to [fork](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo) this repository as opposed to using it as a template. This will also allow you to [synchronize your copy](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork) of template to your fork as well.

Unfortunately, one logistical issue with a template theme like Academic Pages that makes it a little tricky to get bug fixes and updates to the core theme. If you use this template and customize it, you will probably get merge conflicts if you attempt to synchronize. If you want to save your various .yml configuration files and markdown files, you can delete the repository and fork it again. Or you can manually patch.

---
<div align="center">
    
![pages-build-deployment](https://github.com/academicpages/academicpages.github.io/actions/workflows/pages/pages-build-deployment/badge.svg)
[![GitHub contributors](https://img.shields.io/github/contributors/academicpages/academicpages.github.io.svg)](https://github.com/academicpages/academicpages.github.io/graphs/contributors)
[![GitHub release](https://img.shields.io/github/v/release/academicpages/academicpages.github.io)](https://github.com/academicpages/academicpages.github.io/releases/latest)
[![GitHub license](https://img.shields.io/github/license/academicpages/academicpages.github.io?color=blue)](https://github.com/academicpages/academicpages.github.io/blob/master/LICENSE)

[![GitHub stars](https://img.shields.io/github/stars/academicpages/academicpages.github.io)](https://github.com/academicpages/academicpages.github.io)
[![GitHub forks](https://img.shields.io/github/forks/academicpages/academicpages.github.io)](https://github.com/academicpages/academicpages.github.io/fork)
</div>
>>>>>>> gh-pages
