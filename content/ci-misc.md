# Other features 

[cx-examples-GL]: https://gitlab.com/michele.mesiti/cx-course
[cx-examples-GH]: https://github.com/mmesiti/cx-course
[cx-exampler-KIT]: https://gitlab.kit.edu/michele.mesiti/cx-course

In the past episodes we have seen some examples 
of minimal workflows (testing, documentation).

In this episode we focus on specific features.
In this repository ([GiHub][cx-examples-GH],[GiLab][cx-examples-GL])
a number of examples and exercises have been collected,
so that you can try different features.

Fork it on the platform you prefer.

## General ideas

Basic ideas for GitHub Actions and Gitlab CI/CD.


``````{tabs}
`````{group-tab} GitHub Actions
The workflow are defined in `yaml` files 
in `.github/workflows`
(one file - one workflow)

- Typically each push triggers the **workflow**s 
- Each workflow has
  - a list of event triggers (typically `push`, but others are possible, 
    for example `workflow_dispatch`
    which is necessary to launch the workflow manually) 
  - a list of jobs, that run independently, made of steps
    - each job has a list of steps that run one after another
    - steps runs on the same "machine" and share data and context
    - each step can use a pre-defined *action* (see )
      or a script
    - the first step typically is the [checkout action ](https://github.com/marketplace/actions/checkout)
    - steps are executed one after the other. If a step fails, the job fails
  - if a job in a workflow fails, then the workflow fails
- if a workflow fails, then the build is marked as failed.



`````

`````{group-tab} GitLab CI/CD

By default, The pipeline and all its jobs are defined 
in a file named `.gitlab-ci.yml`
at the root of the repository.

- Each push triggers a new **pipeline**
  ([unless skipped](https://docs.gitlab.com/ee/ci/pipelines/\#skip-a-pipeline))
- Each pipeline consist of **stages**.  
  Predefined [stages](https://docs.gitlab.com/ci/yaml/#stages):
  `build`, `test`, `deploy`, (plus `.pre` and `.post`)
- Stages run in sequence 
- Working tree is typically automatically **checked out** at the appropriate commit 
  and **cleaned up** between stages: Use **artifacts** to keep files
- Each stage consist of **jobs**

  - Jobs in the same stage can run in parallel
  - If a job fails then the stage fails, and all subsequent stages are skipped
  - If a stage fails then the pipeline fails


`````
``````

```{note} Editing workflow and pipeline files

Whenever you want to edit a file that defines a workflow (on GitHub)
or a pipeline (on GitLab)
consider doing this directly on the web interface,
as the build-in editors have a linter and autocompletion
which will vastly reduce the probability of making trivial mistakes.

```

## A basic pipeline: Compile and run a C program

This example is available in the example repository,
on the `main` branch.




``````{tabs}
`````{group-tab} GitHub Actions



`````

`````{group-tab} GitLab CI/CD

In this case we have only one stage 
(we actually do not have to list it)
and a single job in that stage that does everything
in a single step:

```{code-block} yaml
stages: 
  - test

doall:
  stage: test
  script: gcc -o hello ./src/hello.c && ./hello
```

`````
``````

## Failures


## Artifacts

## Code reuse

When creating a large workflow or pipeline,
we might be tempted to copy/paste the job definition.
What alternatives do we have?

``````{tabs}
`````{group-tab} GitHub Actions



`````

`````{group-tab} GitLab CI/CD

- Checkout branch `templates`
- have a look at the `.gitlab-ci.yml` file,
  notice the use of `.greet-base` and of the `extends:` key
- How can the base job be customized?

See also: [yaml anchors](https://docs.gitlab.com/ee/ci/yaml/yaml\_optimization.html\#anchors)



`````
``````

## Parametric tasks: Matrix 

Sometimes you might want to run the same job for many different "cases",
or parameters (e.g., different versions of a library/dependency/compiler).

``````{tabs}
`````{group-tab} GitHub Actions
Different variations of a job in a workflow 
can be run by using a [matrix strategy](
https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/run-job-variations
).
`````
`````{group-tab} GitLab CI/CD

Different variations of a job in a workflow can be run in parallel
(or queued, if necessary)
using the [parallel:matrix](https://docs.gitlab.com/ci/yaml/#parallelmatrix)
feature.

For an example and practice, see the [exercise repository][cx-example-GL] on the `matrix` branch.

``````






## Mirroring

A *Mirror* is a copy of a repository 
that is kept *automatically* in sync
while being, typically, on a different forge,
to take advantages of different features, 
or reach different communities.


There are 2 possible techniques:
- *Pull*: the "mirror" repository is configured 
  to automatically pull from the original at regular intervals.
  This is typically a "premium"  feature,
  as it is generally inefficient (*polling*).
- *Push*: the original repository is configured 
  to automatically push to the "mirror" whenever there are changes.
  This is typically more efficient.
 
``````{tabs}
`````{group-tab} GitHub Actions
To set a push mirror, one can use GitHub actions (using the action <https://github.com/wangchucheng/git-repo-sync>)
 
A small guide is available in the `github-to-gitlab-mirror` branch
of the [example repository][cx-examples-GH].

```{exercise} Push mirror from GitHub to GitLab


1. Fork the [example repository][cx-examples-GH] on GitHub.
2. Create and empty repository on a GitLab server (tip: disable notifications)
3. Checkout the `github-to-gitlab-mirror` branch in the example repo
4. Follow the instructions in the README.md to set up the mirroring

```


`````

`````{group-tab} GitLab CI/CD
It is possible to set up mirroring 
following the official instructions:
- for [push](https://docs.gitlab.com/ee/user/project/repository/mirror/push.html).
- for [pull](https://docs.gitlab.com/ee/user/project/repository/mirror/pull.html). This feature might be unavailable.


`````
``````
