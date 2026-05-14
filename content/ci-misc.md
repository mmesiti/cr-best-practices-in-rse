# Other features 

```{objectives}
- Have an idea of what is possible to do with actions or CI, and how
```


[cx-examples-GL]: https://gitlab.com/michele.mesiti/cx-course
[cx-examples-GH]: https://github.com/mmesiti/cx-course
[cx-exampler-KIT]: https://gitlab.kit.edu/michele.mesiti/cx-course

In the past episodes we have seen some examples 
of minimal workflows (testing, documentation).

In this episode we focus on specific features.
In the exercise repository 
(see [link to GiHub version][cx-examples-GH],
[link to GiLab version][cx-examples-GL])
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
  [unless skipped](https://docs.github.com/en/actions/how-tos/manage-workflow-runs/skip-workflow-runs) 
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
    - steps are executed one after the other. 
      If a step fails, 
      the subsequent steps are not executed,
      and the job fails.
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

```{note} **Editing workflow and pipeline files**

Whenever you want to edit a file that defines a workflow (on GitHub)
or a pipeline (on GitLab)
consider doing this directly on the web interface,
as the build-in editors have a linter and autocompletion
which will vastly reduce the probability of making trivial mistakes.

```

```{warning} **Exercises on GitHub**: starting workflows manually

To trigger manually the workflows on GitHub
using your fork of [example repository][cx-examples-GH],
you might have to switch the default branch 
to the appropriate one, in "Settings" -> "General" -> "Default Branch".

```

```{note} **Email notifications**

Both GitHub and GitLab are eager to send you emails 
when a workflow or pipeline fails.

While this is in most case very useful,
consider disabling email notifications on the repository 
you use for the exercises during this session.

```



## A basic pipeline: Compile and run a C program

This example is available in the example repository,
on the `main` branch.


``````{tabs}
`````{group-tab} GitHub Actions

In this case we have a single job,
with 3 steps: checkout, build and run.


```{code-block} yaml
:emphasize-lines: 3,4,8,12
name: BasicExample
on: 
  - push                                  # the workflow runs when we push
  - workflow_dispatch                     # we can launch the workflow manually

jobs:
  build_and_run:
    runs-on: ubuntu-latest                # This is necessary, 
                                          # specifies the kind of host where to run
    steps:
      - name: "Checkout"
        uses: actions/checkout@v6         # We use a pre-defined *action*
      - name: "build"
        run: gcc -o hello ./src/hello.c
      - name: "run"
        run: ./hello
```

Note that `runs-on` takes one or more labels of a runner,
and identifies the type of host.

`````

`````{group-tab} GitLab CI/CD

In this case we have only one stage 
(we actually do not have to list it)
and a single job in that stage that does everything
in two steps:

```{code-block} yaml
:emphasize-lines: 5
stages: 
  - test

doall:
  image: ubuntu:latest
  stage: test
  script: 
    - gcc -o hello ./src/hello.c 
    - ./hello
```

Note: if we do not specify an `image:`, 
then a default one will be chosen.
When we specify one, 
it is downloaded from a registry (typically, dockerhub).

`````
``````

## Failures


Proper reporting and propagation of the failure
of a command in a pipeline or workflow is **paramount**.

**A CI job not properly reporting a failure is itself a severe bug**.

Switch to the branch `failures`.

The shell script 
`./scripts/doesnt-fail-but-typically-should.sh`
shows a typical pitfall.

- How can the problem be fixed?
- What is the default behaviour of on GitHub or GitLab,
  when writing the logic in a workflow/pipeline file 
  instead of a separate shell script?

Switch to the branch `failures`
and follow the instructions in the README,
and have a look at the workflow/pipeline definition file.

## Secrets and repository-specific behaviour

In some cases we want to change the behaviour
of the workflows/pipelines
depending on the repository.

In other cases, we might want to avoid 
storing some information under version control
(it might be not general enough,
or it might be a secret).
but it is needed to run workflows or pipelines.

The solution is to use environment variables 
(also *secrets* on GitHub).

Check out the branch `environment-variables` in the example repository,
to see how to customize a job 
by using environment variables
that are set at the **repository** level. 


## Artifacts

Artifacts are the main way to transfer information 
between jobs in a GitLab pipeline,
and from the runner to the GitLab web interface.


``````{tabs}
`````{group-tab} GitHub

`````
`````{group-tab} GitLab CI/CD
1. Have a look at the `.gitlab-ci.yml` file
2. Try to run the pipeline. Does the behaviour depend on the executor type
   (e.g., docker vs shell)?
3. Look at the jobs in the web interface (on the left, click on Build -> Jobs).
   Notice that the `compilation` job 
   (marked with the `artifact:` key in `.gitlab-ci.yaml`) 
   will have a "Browse Artifact" button.

```{solution}
The job named `execution-without-artifact-download` will fail. 

If one uses a self-hosted runner 
with a `shell` executor for *all* the jobs,
the artifact might not need to be downloaded
as it can still live on the filesystem 
where the runner is located.

*But in this particular case*, since the artifact path 
is inside the repository
and the repository gets cleaned at the start of each job,
the job will not find the necessary file and fail.

```

`````
``````

## Code reuse

When creating a large workflow or pipeline,
we might be tempted to copy/paste the job definition.
What alternatives do we have?

``````{tabs}
`````{group-tab} GitHub Actions


[YAML anchors](https://docs.gitlab.com/ee/ci/yaml/yaml\_optimization.html\#anchors)
also work on GitHub actions (with some limitations compared to GitLab).

In the GitHub context,
actions themselves are the main building block,
and they are reusable by default.


`````

`````{group-tab} GitLab CI/CD

- Checkout branch `templates`
- have a look at the `.gitlab-ci.yml` file,
  notice the use of `.greet-base` and of the `extends:` key
- How can the base job be customized?

See also: [yaml anchors](https://docs.gitlab.com/ee/ci/yaml/yaml\_optimization.html\#anchors)

Another more sophisticated way to reuse code 
are [components](https://docs.gitlab.com/ci/components/),
which follow a philosophy similar to actions 
in the GitHub context.


`````
``````



{ref}`Parametric tasks <parametric-jobs>` 
are also a way to "reuse" code,
or at least to avoid code duplication.

(parametric-jobs)=
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





(mirrors)=
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

When triggered, the workflow on that branch 
pushes the current branch of the repository
to [a mirror on GitLab.com](https://gitlab.com/michele.mesiti/cx-course-mirror-from-github).


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

The [GitHub mirror][cx-examples-GH] and the [KIT GitLab mirror][cx-examples-KIT] are set as a push mirror in the [GitLab repo][cx-examples-GL].


`````
``````

## Conditional execution of workflows, jobs and pipelines

On both platforms 
it is possible to make so that the execution of the jobs, steps 
or of the full workflow
is dependent on some conditions. 

Typical cases are:
- Always: force the execution of jobs/steps no matter what
- Only in case of a merge
- Only in case of a tagged commit
- Only on a particular branch
- based on expressions involving environment variables
- only when some files are changed

``````{tabs}
`````{group-tab} GitHub Actions
`````

`````{group-tab} GitLab CI/CD
[Rules](https://docs.gitlab.com/ci/yaml/#rules) 
can be used to include/exclude jobs in/from pipelines.
`````
``````



# Self-hosting 

There might be situations where you need 
to run the workflows/pipelines on resources you own,
instead of relying on `github.com` or any specific gitlab server.

Possible reasons:
- GitHub actions and GitLab CI/CD on gitlab.com 
  have usage limits (for private repos), 
  or reliability issues,
  and your test suites take too long
- You need to test (or benchmark)
  your code with specific hardware and software
  (e.g., on HPC)

Both gitlab and github will schedule jobs on runners
comparing the tags/labels of the runners
and the tags/labels of the job:
for a job to execute on a given runner
the tags/labels of the job must be a subset 
of the tags/labels of the runner.

``````{tabs}
`````{group-tab} GitHub Actions
One can 
[add](https://docs.github.com/en/actions/how-tos/manage-runners/self-hosted-runners/add-runners)
a self-hosted runner
to a repository.

````{warning}

It is a security risk to have a self-hosted runner
attached to public repositories,
because in that case 
an attacker
could open a merge request 
and run malicious code as a part of a workflow
that gets executed by your self-hosted runner.

Properly managing {ref}`mirrors <mirrors>` 
can alleviate this problem.
````
Most importantly:

- A job launched on the self-hosted runner can access 
  the whole machine/vm/container it is running on,
  and it runs in that context,
  so one might have to start the runner
  inside a container
  (but then, if a job itself 
  requires to execute in a container,
  there might be issues)
- the workflow files might need to be adjusted,
  in particular the value associated to `runs-on`
  for every job (tags)
  need to be a subset 
  of the ones that identify the self-hosted runner.

`````
`````{group-tab} GitLab CI/CD

One can [add](https://docs.gitlab.com/runner/)
a self-hosted runner
to a project.

It is possible to make sure that a self-hosted runner
runs jobs only on *protected branches*
(only few people can make commits on them,
and merge into them).

It is also possible to choose different *executors*,
for example "docker",
which will run jobs inside a container
(but docker must be present on the host).

`````
``````

