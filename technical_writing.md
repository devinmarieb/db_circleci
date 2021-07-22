# Caching vs. Workspaces for Persisting Data

Data can flow in a few different ways with CircleCI. This document will inform you about two different data flows, Workspaces and Caching.

The main difference is whether the persistence of data is needed between a single Workflow, or between the same job in different Workflows.

## Caching
Caching persists data for the same job, between separate Workflows.

The ability to restore as much as possible from a cache in a job run eliminates the need for expensive fetch operations, and keeps the run as fast as possible.

This is particularly useful for large files, like dependencies. Being able to restore dependencies from the cache means the new build will only have to download any new dependencies that have been introduced since the last cache.

Since caches are immutable, moving between Workflows requires some planning to avoid race conditions. To avoid race conditions, you can change job dependencies to specify the hierarchy of jobs.

Caches are global, so you want to be careful about what data is being cached. Any branch in a project will use the cache, so the data needs to be a reliable single source of truth for all branches.

## Workspaces
Workspaces persist data in the same Workflow. The objective is to move data created during a job to subsequent jobs.

Using Workspaces gives you the benefit of having immutable layers so that build process repetition happens smoothy between Workflows.

Contradictions between data in jobs is taken care of through the Workflow graph, where a job only sees upstream Workspace data, and uses that as the source of truth.

If a situation arises where concurrent jobs have a contradiction, the Workflow is flagged with an error and there is a request to check and re-run.

You want to be careful with the data introduced to your Workflow, particularly dependencies. Repeatedly packing and unpacking dependencies costs time. Since the goal is to keep your Workflow as fast as possible, dependencies are better left with the caching process.
