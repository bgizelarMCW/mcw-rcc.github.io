# Using Data in a Job

You will find storage paths that are common on all cluster compute nodes. These include `/tmp` and `/scratch`, which can be used for job files. If you would like more detail about Research Computing storage, please see the [storage guide](../../storage/rcc-storage.md).

## Workflow

Many jobs have specific needs, but there are some best practices that apply in all cases.

- Always run a test job (preferably a small sample) to verify storage requirements
- Always check your quota to ensure sufficient storage space
- Always read/write data from fastest storage available
- Always write high I/O output to `/scratch`

For best performance, job files should be located on `/scratch`. We suggest the following workflow.

1. Create a workspace in `/scratch` with `ws_allocate`, then copy your input data from `/group` into it.
2. Submit your job from the workspace, reading from and writing to it. The job's `--time` must end before the workspace expires, or the job is rejected (see [Job Time Limits and Workspace Expiration](running-jobs.md#job-time-limits-and-workspace-expiration)).
3. When the job finishes, copy the results you want to keep back to `/group`.
4. When your project work is done, release the workspace with `ws_release`. If you forget, it expires on its own (default: 60 days).

!!! tip "Scratch space is organized into workspaces"
    See [Workspaces](../../storage/workspaces.md) for how to create, extend, release and restore them.

--8<-- "includes/abbreviations.md"
