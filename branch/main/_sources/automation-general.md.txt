# Automation on Forges and CI

[GitHub Actions](https://github.com/features/actions/) and [GitLab CI/CD](https://docs.gitlab.com/ci/)
can automatically perform arbitrary operations, 
typically when someone  `push`es to a repository.

These are used for:
- gatekeeping/enforcing coding standard on contributions from others
- parts of the development cycle that do not fit on the machine you use to develop code.

Which typically means:

- Build and publish your work on the web (including documentation)
- Run *all* unit and integration tests, and static analysis tools
- Measure test coverage
- Check code style and practices ({term}`Linting <linting>`, auto-formatting)



`````{discussion} Feedback loops in Software Development
Consider these activities we can perform on a software project:

- type checks             
- incremental compilation 
- full compilation        
- test suite (unit)       
- test suite (end-to-end) 
- linting                 
- code format check       
- building documentation  
- spell checking          


For each of these activities:

- How much information can it give us while developing software?
- How "expensive" is it?
- How important is it?
- Should it be done on a software forge, or on the machine
where you develop? 
- How often should it be performed?

What about test coverage?

````{solution} Writer's recommendation (but let us discuss first)


```{list-table} Urgency and Importance of Practices in Software Development
:widths: 30 15 15 15 25
:header-rows: 1
:stub-columns: 1


* - **Practice**
  - **Information Gain**
  - **Time Cost**
  - **Importance**
  - **Where and When?**
* - type checks
  - <span style="color:green"> medium </span>
  - <span style="color:blue">low</span>
  - <span style="color:blue">low</span> 
  - local, remote  
    (commit hooks?)
* - incremental  
    compilation
  - <span style="color:green"> medium </span>
  - <span style="color:blue">low</span> 
  - <span style="color:blue">low</span> 
  - local
* - full compilation
  - <span style="color:green"> medium </span>
  - <span style="color:red">high</span>
  - <span style="color:red">high</span>
  - remote (local)
* - test suite (unit)
  - <span style="color:green"> medium </span>
  - <span style="color:red">high</span>
  - <span style="color:orange">very high</span>
  - local,remote
* - test suite  
    (end-to-end)
  - <span style="color:red">high</span> 
  - <span style="color:red">high</span>
  - <span style="color:orange">very high</span>
  - remote (merge?)  
    local (sparsely)
* - {term}`linting`
  - <span style="color:green">medium</span>
  - <span style="color:blue">low</span>
  - <span style="color:green">medium</span>
  - local, remote  
    (commit hooks)
* - building  
    documentation
  - <span style="color:blue">low</span>
  - <span style="color:green">medium</span> 
  - <span style="color:red">high</span> 
  - remote
* - spell check
  - <span style="color:blue">low</span>
  - <span style="color:blue">low</span>
  - <span style="color:green">medium</span>
  - remote (local)
    
```

Typically test coverage measurement and display can be performed remotely.
Coverage data is gathered when running a test suite.

````
`````

## Git Hooks

Git has a mechanism to trigger actions when some event happen,
called [*hooks*](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks).

Most notable hooks:

- `pre-commit`: launches a process when you launch the command `git commit`.
   If the process terminates with a non-success exit code, 
   it will prevent you from committing your changes.
   Typical use cases:
   
   - auto formatting and formatting checks
   - {term}`linting`
  
   ```{note}
   There is a Python framework called [pre-commit](https://pre-commit.com) 
   created to manage (e.g., install and run) useful pre-commit hooks.
   ```

- `post-receive`: this hook can be used to launch a process 
   on the machine where the remote repository is hosted,
   as a fundamental form of CI.
   
   

```````{exercise} Basic CX with hooks
   
We can reproduce the fundamental functionality of automation platforms
by setting up a bare repository on a remote machine
and the relevant hooks.


For this exercise:
1. clone [this repository](https://gitlab.com/michele.mesiti/cx-course.git):

```
git clone https://gitlab.com/michele.mesiti/cx-course.git
```

2. switch to the `hooks` branch
3. follow the instructions in the 
   [README](https://gitlab.com/michele.mesiti/cx-course/-/tree/hooks?ref_type=heads).

Start with the "local" version of the exercise,
then - if you have a remote machine you can connect to via SSH - 
try the "remote" version.

Note that this simple mechanism is very limited,
which is why in most cases automation platforms like 
GitHub actions or Gitlab CI/CD are used.

```````


```{callout} "CI"  and  HPC

Some issues in the development of HPC code 
are addressed by regularly running jobs that:

- Test compilation with different compilers and libraries (e.g., MPIs, CUDA/ROCm, ...)
- Test and **benchmark the performance** on specific hardware and infrastructure
- build/package software for various compilers, MPI versions, architectures
- ... all using standardized environments in conjunction with containers

This might require "special" set up,
especially performance benchmarking.


```


