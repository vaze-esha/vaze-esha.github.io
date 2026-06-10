---
layout: post
title: "Notes for Data Analysis"
date: 2025-03-01
---

## Introduction

Last term, I collected every question that my classmates asked me (as they worked on their theses), and compiled notes to make a little guide that could (potentially) be useful for someone working with economic data for the first time. The primary purpose of this document is to give you some guidance on how to organize your project and manage your do-files in a way that is reproducible and clean.

I'll go through some basic do-file structuring practices to make your workflow easier to follow if this is your first time touching a large-ish project in Stata. Even if you feel that replication is not paramount for an undergraduate project (and perhaps it isn't), it's still useful to organize your data with that goal in mind, both for your own convenience and for that of your readers'.

I wrote this guide because most undergraduates who don't work as RAs haven't had the pleasure of reading [Code and Data for the Social Sciences: A Practitioner's Guide](https://web.stanford.edu/~gentzkow/research/CodeAndData.xhtml), and besides, the scale of most undergraduate projects is smaller and the scope much narrower. This guide caters to that small corner of writing code for economics, and is easier to follow (especially if you're not in the business of writing shell scripts for automation) because it only assumes a basic working knowledge of Stata.

---

*Note:* There is no dearth of resources that are specifically geared towards writing code for the social sciences (see [here](https://github.com/jdingel/projecttemplate), [here](http://plain-text.co/), and also [here](https://www.youtube.com/watch?v=ZSunU9GQdcI)). This guide is not an attempt to reinvent that (nearly) perfect wheel. I wrote this because many people I know found existing resources to be too broad in scope to serve as a meaningful reference for their 2-term projects. All the resources above (and some more at the end of this document) are excellent for those truly wrestling with big data and teams. However, if you're trying to get through a thesis class (or just doing a small project to satisfy your curiosity), then I suspect this guide will be of some use to you.

---

Why is this guide focused on Stata? The target audience for this guide is an economics undergraduate writing their first paper/project, and it is highly likely that they will use Stata (easy, caters to economics, recommended by instructors).[^1]

---

*Side-note:* Stata is weird, and has an overly specific use case (academic economics). The error messages are largely useless, the documentation is lean, the online forums are a scary place (I'm looking at you, Statalist), and most LLM chatbots perform unsatisfactorily when asked to write Stata code (clunky, inefficient, and ugly to look at). I'm not sure what the future looks like for Stata, but it's been here long enough, and for all my complaining it is very convenient for the niche it serves. That said, I don't think I would bother touching it if I wasn't enrolled in an economics degree.

---

My main purpose here is to introduce a replicable format of code and data organization for smaller projects, the knowledge for which is primarily borrowed from my time as a research assistant, spent working on replication packets for papers. Per usual, all errors are my own.

To make this easy to follow, I describe a hypothetical project, and then show you how I would organize it. I try to keep this guide as linear as possible, although that is very rarely the case when you actually start getting your hands dirty.

## Data Organization

**Hypothetical project**: We're trying to measure the impact of an employment guarantee program on female unemployment rates in the Canadian province of Newfoundland. Assume that we live in a perfect world where the program assignment was randomized, and the government gives us a full dataset of treated and control populations with their demographic details. To help our analysis, we download a zip-code level map of Newfoundland, and a dataset containing household income, number of children and the addresses of everyone enrolled in the program.

These datasets constitute our "raw inputs". We will create a project directory on Dropbox (or any other file-hosting service that you prefer) where all our data will live.[^2]

Here's what a typical data directory would look like:

```
project_newfoundland_data       # main data directory
├── 0_raw_input                 # raw data directory
│   ├── 0_1_rct.xlsx
│   ├── 0_2_zip_codes.shp
│   └── 0_3_income_and_covariates.xlsx
├── 1_intermediate              # processed data directory
│   ├── 1_1_rct_cleaned.dta
│   ├── 1_2_zip_codes_rct_merged.dta
│   └── 1_3_covariates_rct_merged.dta
└── 2_final                     # datasets used for final analysis
   └── 2_1_rct_analysis.dta    # contains variables for analysis
                               # folder typically contains final datasets used for regressions, tables and figures
```

Sub-directory `1_intermediate` contains data that has been cleaned (drop duplicate observations, harmonize variables, address consistency issues) and `2_final` contains the processed datasets for your final analysis. When you upload your replication packet, the only datasets that you (ideally) include in it are your raw inputs, and let your code generate the rest.

This makes your data organization easy to follow for someone who has limited context about the project, while also making it convenient for your future self to follow your data cleaning methodology.[^3]

---

*Aside:* Even when you first start, organize your project with a replication package in mind. Which means two things:

1. A complete stranger, given reasonable instructions, should be able to replicate every result in your project by running your code locally.
2. Said stranger (assuming that they have non-zero Stata knowledge) should understand your code, or at the very least be able to parse the rudiments of your data analysis.

Why should you care? First, if there are some glaring econometric issues with how you analyze your data, well documented and replicable files will help someone point them out (and save you tons of trouble). Second, future you is effectively a complete stranger (you will forget why you used that specific transformation two summers ago). Third, it's good for accountability: you should care about doing research that is transparent and replicable. Think of it as creating a public good.

## Code Organization

The way you organize your code mimics the data storage structure. I recommend creating a repository on GitHub with relevant sub-directories to store all your code.[^4]

```
project_newfoundland_do  # main code directory
├── setup.do   
├── master.do
├── 0_import_and_process       # import and process data
│   ├── 0_1_rct_import.do
│   ├── 0_2_zip_codes_import.do
│   └── 0_3_income_and_covariates_import.do
├── 1_intermediate             # processed data directory
│   ├── 1_1_rct_cleaning.do
│   ├── 1_2_create_dataset.do
│   └── 1_3_covariates_rct_create_dataset.do  # create your datasets
│                                             # files here correspond (see numbering) to datasets that they produce as outputs
│                                             # this keeps things easy to keep track of
└── 2_final                    # datasets used for final analysis
   ├── 2_1_rct_analysis.do    # your main analysis file
   └── 2_2_figures_maps.do    # some figures for your paper
```

---

So what are `master.do` and `setup.do`? You want a stranger to be able to replicate this code by running a single file (literally at the push of a button), and a master do-file lets you do that. Now that all your data and code is stored based on its use-case, here are two files you should consider adding to make this workflow easier to run:[^5]

## `setup.do`

More often than not, people do the following:

```
use "/User/john_doe/Dropbox/project_newfoundland_data/0_raw_input/0_1_rct.xlsx"
…
use "/User/john_doe/Dropbox/project_newfoundland_data/1_intermediate/1_1_rct_cleaned.dta"
…
use "/User/john_doe/Dropbox/project_newfoundland_data/2_final/2_1_rct_analysis.dta"
```

And on and on. You get the idea. While there's nothing wrong with it (your code will run), the repetition alone should ring some alarm bells. Surely there must be a way to make this less clunky (there is). **Here's what you can do**: create a do-file that lives in your main code repository and dump all your directory paths into it. Ideally, you want to be able to use this file across multiple sub-directories for your project, so the paths you use are "global" macros (read up on local and global scope in Stata to better understand what you need)

```
global $workingdir "/User/john_doe/Dropbox/project_newfoundland_data"
global $raw_input_dir "$workingdir/0_raw_input"
global $intermediate_data_dir "$workingdir/1_intermediate_data"
…
```

Now you're free to use:

```
use "$raw_input_dir/0_1_rct.xlsx"
```

Which saves some space, looks better, and is much more convenient than printing the whole file path over and over. For a typical project, I would create a setup file that sets all my global data directories, installs any package dependencies, sets any locals that I need, and so on.

## `master.do`

A master do-file sequentially calls every do-file in your project. When you're working with larger projects, they're convenient because they save you the trouble of having to individually run files. Coming back to replication, when you finally make your project publicly available your master file lets people run your code at the click of a button instead of having to figure out the right order needed to generate your results. It looks something like this:

```
global $dodir "User/john_doe/project_newfoundland_do" # point to wherever John cloned his repository, add to setup file
…
do "$dodir/2_analysis/2_1_rct_analysis.do"
do "$dodir/2_analysis/2_2_figures_maps.do"
...
```

Some people also like having their paths in the master file instead of in `setup.do` – it's really up to you, and both of these steps are technically optional, but they make life easier and your code more legible in the long and short run. If you want even more control, you can make a master file for each code sub-directory to run each section of your data cleaning and analysis separately.

Great. Now you have neatly organized boxes, and specific uses for them all. Write down exactly what's in each file, each directory, each sub-directory (Make a README file, make two, make four). Be as detailed as possible, and then cut the fluff later when you feel like you have a better handle on how everything works.

When making your do-files, write comments. Be verbose. Try to make everything as simple as possible, and then go back again and make it simpler. If you find yourself repeating the same line of code over and over, there's a better way to do it. It always helps to imagine a complete stranger who knows nothing about your project, and then write your code so that given minimal context they're able to make sense of what it does.[^6]

---

**Closing Notes:**
A large part of this guide was born out of frustration from watching people clear-reload their datasets from the depths of their downloads folder every single time they ran a regression. I realized that it would be useful (not to my cohort, but perhaps future ones) to have a short text that tells you how to structure your project so that it's easier for you, and whoever is helping you to understand it. This is, as stated previously, a very bare-bones guide and should work fine for a small to medium sized project. Eventually, you'll want to learn how to automate more of your workflow — you can learn basic shell-scripting (here's a fun [book](https://tldp.org/LDP/abs/html/index.html)), or just use Python. As your projects get larger, you'll even want to automate things like importing raw-inputs from a directory (it's also fun, even when you don't have to do it — I learnt how to use my computer pretty late in life, and I'm still fascinated by how cool and easy it is to automate really simple tasks).[^7] If you're comfortable following a document less hand-holdy (and more general), then you should read [Code and Data for the Social Sciences](https://web.stanford.edu/~gentzkow/research/CodeAndData.xhtml).

Other fun resources you can look at (if you're interested in the general concept of reproducible research):

1. [Institute for Replication](https://i4replication.org/about.html)
2. Browse AER papers' replication packages (for inspiration)

## Further Reading

Some additional resources I use and like

1. [Coding for Economists: A Language-Agnostic Guide to Programming for Economists](https://ljristovska.com/assets/docs/coding_for_econs_20190221.pdf)
2. [An Economist's Guide to Visualizing Data](https://www.aeaweb.org/articles?id=10.1257/jep.28.1.209)
3. [Evidence on Research Transparency in Economics](https://pubs.aeaweb.org/doi/pdf/10.1257/jep.35.3.193)
4. Nick Hagerty has a cool course on [advanced data analytics in R](https://github.com/msu-econ-data-analytics/course-materials#external-resources), with a [data cleaning checklist](https://raw.githack.com/msu-econ-data-analytics/course-materials/main/data-cleaning-checklist/Data-Cleaning-Checklist.pdf) that's concise and useful.

[^1]: You're more than welcome to use it for organizing your code and analysis in any other language. This is not a "how to write code for (undergraduate) economic data analysis guide", so the general principles extend to pretty much whatever language you choose to work in.
[^2]: You can also do this locally, especially if you're the only one working on the project.
[^3]: Even if you're like me and claim you have perfect memory, as your project gets larger and contains a mountain of data it's better to have it corralled away into specific boxes than not. The added convenience comes from the fact that your code generates everything except the raw inputs, so you can make edits to your analysis more conveniently as time passes.
[^4]: Even if you're the only one working on the project, it's incredibly useful to have a record of the changes you make. Version control will save you time and keep you accountable. If it makes life easier (and you don't want to go through the mental gymnastics of learning command line functionality to use GitHub), consider getting GitHub Desktop to manage your do-files.
[^5]: Sorry for flip-flopping between 0/1 indexing, but that's just how I number these. You probably want to change that based on your philosophical and aesthetic bent.
[^6]: They don't have to know it exactly, but if you're running a 2SLS design with logs and lags then you should specify why, and document each variable transformation you make along the way.
[^7]: You might need to import hundreds of excel sheets from a government database that is regularly updated. Doing this manually would be both painful and a waste of time.
