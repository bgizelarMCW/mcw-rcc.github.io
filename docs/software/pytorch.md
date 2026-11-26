# PyTorch

PyTorch can be run in batch, interactive, or Jupyter Notebook. For more information, check the module help information with `module help pytorch`.

## PyTorch job

The following example will use PyTorch to train a network on the MNIST data set.

First, create a [workspace](../storage/workspaces.md) for the job and download the PyTorch examples into it:

```bash
ws_allocate pytorch-test
cd "$(ws_find pytorch-test)"
git clone https://github.com/pytorch/examples.git
```

Now that you have the examples, save the following job script in the workspace to train the network.

<!-- markdownlint-disable MD046 -->
=== "pytorch-mnist.slurm"
    ```txt
    #!/bin/bash
    #SBATCH --job-name=pytorch
    #SBATCH --ntasks=1
    #SBATCH --time=01:00:00
    #SBATCH --account={PI_NetID}
    #SBATCH --partition=gpu
    #SBATCH --gres=gpu:1
    #SBATCH --output=%x-%j.out

    module load pytorch
    python examples/mnist/main.py >> output.log  
    ```
<!-- markdownlint-enable MD046 -->

Submit the job:

```bash
cd "$(ws_find pytorch-test)" && sbatch pytorch-mnist.slurm
```

## PyTorch Jupyter Notebook

This functionality is now provided by Open OnDemand! Use the [Jupyter Notebook app](https://ondemand.rcc.mcw.edu/pun/sys/dashboard/batch_connect/sys/bc_hpc_jupyter/session_contexts/new) to start a PyTorch enabled kernel.

--8<-- "includes/abbreviations.md"
