---
title: "Accessing software"
teaching: 15
exercises: 10
questions:
- "How do we load software packages?"
objectives:
- "Understand how to load and use a software package."
keypoints:
- "Load software with `module load softwareName`"
- "The module system handles software versioning and package conflicts for you automatically."

---

On a high-performance computing system, it is often the case that no software is loaded by default. If we want to use a
software package, we will need to "load" it ourselves.

Before we start using individual software packages, however, we should understand the reasoning
behind this approach. The three biggest factors are:

- software incompatibilities;
- versioning;
- dependencies.

Software incompatibility is a major headache for programmers. Sometimes the presence (or absence) of
a software package will break others that depend on it. Two of the most famous examples are Python 2
and 3 and C compiler versions. Python 3 famously provides a `python` command that conflicts with
that provided by Python 2. Software compiled against a newer version of the C libraries and then
used when they are not present will result in a nasty `'GLIBCXX_3.4.20' not found` error, for
instance.

Software versioning is another common issue. A team might depend on a certain package version for
their research project - if the software version was to change (for instance, if a package was
updated), it might affect their results. Having access to multiple software versions allow a set of
researchers to prevent software versioning issues from affecting their results.

Dependencies are where a particular software package (or even a particular version)
depends on having access to another software package (or even a particular version of
another software package). For example, the VASP materials science software may 
depend on having a particular version of the FFTW (Fastest Fourer Transform in the West)
software library available for it to work.

## Environment modules

Environment modules are the solution to these problems.
A *module* is a self-contained description of a software package - 
it contains the settings required to run a software packace 
and, usually, encodes required dependencies on other software packages.

There are a number of different environment module implementations commonly
used on HPC systems: the two most common are *TCL modules* and *Lmod*. Both of
these use similar syntax and the concepts are the same so learning to use one will
allow you to use whichever is installed on the system you are using. In both 
implementations the `module` command is used to interact with environment modules. An
additional subcommand is usually added to the command to specify what you want to do. For a list
of subcommands you can use `module -h` or `module help`. As for all commands, you can 
access the full help on the *man* pages with `man module`.

On login you may start out with a default set of modules loaded or you may start out
with an empty environment, this depends on the setup of the system you are using.

### Listing currently loaded modules

You can use the `module list` command to see which modules you currently have loaded
in your environment. If you have no modules loaded, you will see a message telling you
so

```
{{ site.host_prompt }} module list
```
{: .bash}
```
No Modulefiles Currently Loaded.
```
{: .output}

### Listing available modules

To see available software modules, use `module avail`

```
{{ site.host_prompt }} module avail
```
{: .bash}
```
{% include /snippets/14/module_avail.snip %}
```
{: .output}

## Loading and unloading software

To load a software module, use `module load`.
In this example we will use fastqc

Initially, fastqc is not loaded. 
We can test this by using the `which` command.
`which` looks for programs the same way that Bash does,
so we can use it to tell us where a particular piece of software is stored.

```
{{ site.host_prompt }} which fastqc
```
{: .bash}
```
{% include /snippets/14/which_missing.snip %}
```
{: .output}

We can load the `fastqc` command with `module load`:

```
{% include /snippets/14/load_python.snip %}
```
{: .bash}
```
{% include /snippets/14/which_python.snip %}
```
{: .output}

So, what just happened?

To understand the output, first we need to understand the nature of the `$PATH` environment
variable. `$PATH` is a special environment variable that controls where a UNIX system looks for
software. Specifically `$PATH` is a list of directories (separated by `:`) that the OS searches
through for a command before giving up and telling us it can't find it. As with all environment
variables we can print it out using `echo`.

```
{{ site.host_prompt }} echo $PATH
```
{: .bash}
```
{% include /snippets/14/path.snip %}
```
{: .output}

You'll notice a similarity to the output of the `which` command. In this case, there's only one
difference: the different directory at the beginning. When we ran the `module load` command,
it added a directory to the beginning of our `$PATH`. Let's examine what's there:

```
{% include /snippets/14/ls_dir.snip %}
```
{: .bash}
```
{% include /snippets/14/ls_dir_output.snip %}
```
{: .output}

Taking this to it's conclusion, `module load` will add software to your `$PATH`. It "loads"
software. A special note on this - depending on which version of the `module` program that is
installed at your site, `module load` will also load required software dependencies.

## Software versioning

So far, we've learned how to load and unload software packages. This is very useful. However, we
have not yet addressed the issue of software versioning. At some point or other, you will run into
issues where only one particular version of some software will be suitable. Perhaps a key bugfix
only happened in a certain version, or version X broke compatibility with a file format you use. It is also good practice to record which software versions you use for reproducibility. In
all of these example cases, it helps to be very specific about what software is loaded.

Let's examine the output of `module avail` more closely.

```
{{ site.host_prompt }} module avail
```
{: .bash}
```
{% include /snippets/14/module_avail.snip %}
```
{: .output}

We can see the version after the software name and specify the exact version we want to load with `module avail softwareName/version` 
