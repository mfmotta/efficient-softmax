# efficient-softmax

The [Flash Attention](https://arxiv.org/abs/2205.14135) paper references a technique to optimize the computation of the Softmax, developed in [Online normalizer calculation for softmax](https://arxiv.org/pdf/1805.02867). When going through this paper, I thought it would be fun to have its ideas in a Jupyter notebook. But is there a way to compile and execute C++, or CUDA, with Jupyter notebooks? It turns out that -- yes! with [Xeus Cling](https://github.com/jupyter-xeus/xeus-cling) So here I'll describe how I got this to work.

# Xeus Cling setup

This is the setup I've decided to use, so the steps presented here do not cover all possible installation settings. If you have docker installed, you can probably just go for it. Let me know if you have any issues.

1 - Install [mamba](https://mamba.readthedocs.io/en/latest/mamba-installation.html#mamba-install)

I will go with the docker image. This command will pull it and show some information:

```docker run -it --rm condaforge/mambaforge:latest mamba info
```

Now I'll run the container, which I name ``softmax`` in detached mode and execute it. Check the docker documentation for more information about the flags.

```
docker run -v /path-to-project/:/home/efficient-softmax --gpus all -d --name softmax -e USER=$USER -w /home -it --rm condaforge/mambaforge:latest /bin/bash
docker exec -it softmax /bin/bash
```

2 - Install [Xeus](https://github.com/jupyter-xeus/xeus) with

```
mamba install xeus -c conda-forge
```

3 - Install [Cling]( https://github.com/jupyter-xeus/xeus-cling) 

You might want to create a dedicated environment with

```
mamba create -n cling  
source activate cling
```

and then install it

```
mamba install xeus-cling -c conda-forge
```

4 - Install [JupyterLab]( https://pypi.org/project/jupyterlab/)

```
mamba install -c conda-forge jupyterlab
```

  - [Installing the Kernel Spec](https://xeus-cling.readthedocs.io/en/latest/installation.html#installing-the-kernel-spec:~:text=nmake%0Anmake%20install-,Installing%20the%20Kernel%20Spec,-%C2%B6)

If the steps are perfomed as above, you won't find the kernels under ``/opt/conda/share/jupyter/kernels``, but likely in ``/opt/conda/envs/cling/share/jupyter/kernels/``. To install them, run

```
jupyter kernelspec install /opt/conda/envs/cling/share/jupyter/kernels/xcpp11 --sys-prefix
jupyter kernelspec install /opt/conda/envs/cling/share/jupyter/kernels/xcpp14 --sys-prefix
jupyter kernelspec install /opt/conda/envs/cling/share/jupyter/kernels/xcpp17 --sys-prefix
```

5 - Install cuda -- if you haven't already

```
conda install cuda -c nvidia
```
 

