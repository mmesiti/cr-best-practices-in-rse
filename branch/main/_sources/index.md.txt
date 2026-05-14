# Best practices in Research Software Engineering

In this course we will discuss 
some tools and practices 
useful in software development
(in particular, Research Software Engineering).

The material is derived from the CodeRefinery lessons
([documentation](https://coderefinery.github.io/documentation) 
and [testing](https://coderefinery.github.io/testing))
and prepared for the NHR Summer School in Karlsruhe.

This material is divided in two parts:
- In the first part, we will see tools 
  and best practices to write 
  automated tests for our code, 
  and to write and maintain 
  its documentation
- In the second part,
  which tries to be as independent from the first as possible,
  we will see how to increase automation,
  by making use of infrastructure
  offered on software forges 
  GitHub and GitLab.


```{prereq}
   1. Basic understanding of Git.

   2. For the Sphinx part, You need to have
      [sphinx](https://www.sphinx-doc.org) and [sphinx_rtd_theme](https://sphinx-rtd-theme.readthedocs.io/) installed
      (they are part of the [coderefinery environment](https://coderefinery.github.io/installation/conda-environment/)).

   3. You need `pytest` <http://doc.pytest.org> (as part of Anaconda or Miniconda or Virtual Environment).

   4. (Optional) To work on exercises in other languages than Python,
      please follow the instructions under "Language-specific
      instructions" in the `Test design episode <https://coderefinery.github.io/testing/test-design/>`__
      to install the recommended testing frameworks.

If you wish to follow in the terminal and are new to the command line, 
CodeRefinery has recorded a [short shell crash course](https://youtu.be/xbTTDLA3txI).

```


## Part 1: Best practices in Research Software Engineering

### Documentation
In this lesson we will discuss different solutions for implementing and
deploying code documentation.
This demonstration will be mostly **independent of programming languages**
You will be able to follow and do all the exercises
using the [CodeRefinery conda environment](https://coderefinery.github.io/installation/conda/)).


Then we will discuss the form of documentation which is the most immediate to write:
**in-code** comments, and docstrings.

We will step up our game and discuss **what makes a good README**. For many
projects, a curated README is enough.

We will then learn how to build documentation with the
**documentation generator** [Sphinx](https://www.sphinx-doc.org).

We will discuss best practices, and briefly discuss the available tools.

```{toctree}
:maxdepth: 1

in-code-documentation.md
writing-readme-files.md
sphinx.md
tools.md
wishlist.md
summary.md
```


### Testing 

Have you ever had some of these problems?:

- You change B and C, and suddenly A doesn't work anymore.  Time
  wasted trying to figure out what changed.
- There was some simple problem, systematically testing could have
  found it.
- You get someone else's code and are afraid to touch it because who
  knows what might break.  Plot twist: it's your own code!

People have learned that some automatic way 
to check problems makes
software development much easier.  
This part of the lesson will talk 
about the places it's useful for research code, 
and how easy it can be.
We will discuss why testing often needs 
to be part of the software development cycle 
and how such a cycle can be implemented. 
We will see how automated testing works 
and practice designing and writing tests.

```{toctree}
:maxdepth: 1

motivation.md
locally.md
test-design
conclusions

```




# Part 2: Automation on Forges and CI

Platforms like GitHub and GitLab offer services
that can be used to automate parts of software 
development and maintenance.

We will see how to
- trigger remote pipelines
- run automatically 
  the test suite of our code
- deploy documentation to [GitHub Pages](https://pages.github.com)
  and [GitLab Pages](https://about.gitlab.com/features/pages/).

We will also discuss other possibilities 
these automation platform offers,
and some additional, useful features.



```{prereq}
You need an account on the forge you intend to use (GitHub.com, or a GitLab server of your choice).

```

```{toctree}
:maxdepth: 1
:caption: Part 2: Automation on Software Forges and CI

automation-general.md
continuous-integration.md
full-cycle-ci.md
gh_workflow.md
gh-pages.md
ci-misc.md
```

## Other Material



```{toctree}
:maxdepth: 1
:caption: Reference

Shell crash course <https://youtu.be/xbTTDLA3txI>
quick-reference.md
exercises.md
guide.md
license.md
```



```{toctree}
:maxdepth: 1
:caption: About

All lessons <https://coderefinery.org/lessons/core/>
CodeRefinery <https://coderefinery.org/>
Reusing <https://coderefinery.org/lessons/reusing/>
```
