# Maru

## What

Maru is a tiny self-hosting lisp dialect: a symbolic expression evaluator
that can compile its own implementation language.

This repo is also the place of exploration in the land of bootstrapping
and computing system development, by both growing *and* shrinking what's
currently known as Maru.

## How

Maru's architecture is described in Ian Piumarta's paper:
[Open, extensible composition models](https://www.piumarta.com/freeco11/freeco11-piumarta-oecm.pdf).

> it is a sketch of how Maru's generalised eval works,
> which is entirely accurate in intent and approach,
> if a little different in some implementation details

### The Parts

* `eval.c` (in branch `stage-0.c99`) contains a reader/evaluator for a simple
s-expression language, written in C ([C99](https://en.wikipedia.org/wiki/C99)).

* `eval.l` contains the same evaluator, written in the s-expression language.
In other words, `eval.l` is a metacircular evaluator for the language it is written in.

* `emit.l` contains a compiler from s-expressions to [IA-32](https://en.wikipedia.org/wiki/IA-32) (x86)
machine code, written in the s-expression language. This compiler can be thought of as a
semantics-preserving "level shift" from s-expressions to IA-32 machine code, letting the metacircular
evaluator in `eval.l` escape from "infinite metacircular regression" to a language grounded in hardware.
A possible metaphor of this is a "parent universe" that, when compiling the abstract to the concrete,
provides you with a set of axiomatic foundations to build upon. Another possible such "parent universe" is
C99 (I'm planning to work next on an `emit-c99.l`).

* `boot.l` contains some basic data structures, algorithms, and paradigms that are needed by `emit.l`,
written in the s-expression language.

The recursive implementation and compiler (in the .l files) is split into three parts for clarity,
but it could all be in a single source file.

### Build architecture, git repo layout

The bootstrap stages are in separate git branches with the following
naming convention (without a `master` branch):

`[language name].[bootstrap stage]`, e.g `maru.1`.

Optionally, for stage zero in the bootstrap, it also includes the name of the
parent language, from which this "bootstrap sprout" grows:

`[language name].[bootstrap stage].[parent language]`, e.g. `maru.0.c99`, which holds
the bootstrap implementation written in C.

The bootstrap process in general is planned to be like this: stage `n` requests
the/a parent stage (typically stage `(n-1)` of the same language) to compile an
`eval` executable. Then it uses that executable to compile itself (and provide
the foundations for stage `(n+1)`).

During the build the bootstrap stages are `git checkout`'ed under `build/`
and they are built there. A new stage needs to be opened when you want to use a new feature
of the language in the code implementing the language itself.

My plan is not only to grow, but also to *shrink* the languages (i.e. introduce
"negative" bootstrap stages). This will be part of the collaboration with
Daniel A. Nagy's [seedling](https://github.com/nagydani/seedling/) project.

### Build instructions

From the default branch (currently `maru.1`) invoke `make bootstrap`.

## Who

Initially written by [Ian Piumarta](https://www.piumarta.com/software/maru/).

This repo is maintained by [attila@lendvai.name](mailto:attila@lendvai.name).

## Why

* Programming badly needs better foundations, and Maru is part of this exploration.
The foundations should get smaller, simpler, more self-contained, and more approachable
by people who set out to learn programming.

* Repos and build infrastructures should capture the growth of a language, both for
educational, and also for practical reasons: to have a minimal *seed* that is simple to
port to a new architecture, and then have a self-contained bootstrap process that
can "grow" the entire system on top of the new foundation (hardware).

* Maru is very small: in about 1700 lines of code it can self-host
(with about 2300 LoC of throwaway C code for the bootstrap).

* Ian seems to have stopped working on Maru, but it's an interesting piece of code that deserves
a repo and a maintainer.

## Status


### History

#### Around 2010-2013
Maru was developed as part of Alan Kay's *Fundamentals of New Computing* project,
by the *[Viewpoints Research Institute](https://web.archive.org/web/20111002111856/http://www.viewpointsresearch.org/fonc_wiki/index.php/Idst)*.
The goal of the project was to implement an entirely new, self-hosting computing system, with GUI, in 20.000 lines of code.

At some point VPRI went quiet and closed down in 2018. Much of their online content disappeared, and the team (probably) also dissolved.

Their annual reports:
[2007](http://www.vpri.org/pdf/tr2007008_steps.pdf),
[2008](http://www.vpri.org/pdf/tr2008004_steps08.pdf),
[2009](http://www.vpri.org/pdf/tr2009016_steps09.pdf),
[2010](http://www.vpri.org/pdf/tr2010004_steps10.pdf),
[2011](http://www.vpri.org/pdf/tr2011004_steps11.pdf),
[2012](http://www.vpri.org/pdf/tr2012001_steps.pdf).

This git repo is a conversion of Ian Piumarta's mercurial repo that was once
available at http://piumarta.com/hg/maru/, whose contents are preserved in the
branch called `piumarta`. The plan is to eventually revive most of the goodies
available there in a more organized/approachable manner, and also pay attention
to the bootstrapping issues.

#### This git repo

To the best of my knowledge this repo holds the latest published state of
Ian's work (captured in the branch called `piumarta`).

This repo has received, and will receive forced updates until I come up with
a build setup that nicely facilitates bootstrapping (in both directions:
growing and simplifying the language), and multiple, parallel paths of these
bootstrapping branches of language development (except the `piumarta` branch).

There were two Mercurial repositories, one for the VPRI demos, and one for the
minimal self-hosting Maru (? it's just an assumption of mine). I based my work
on top of the minimal repo, which was created by Ian in 2011.
