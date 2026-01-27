# HPC Tips
For your final projects, you will most likely need to work outside of Jupyter/Python notebooks, and may need GPUs to assist in model training. For this, you can use [NYU HPC](https://sites.google.com/nyu.edu/nyu-hpc/)! The original [documentation](https://sites.google.com/nyu.edu/nyu-hpc/) is decent, but here I'll provide some tips on my workflow specifically. To start, you'll need to request access to an HPC account if you don't have that already. You can follow the steps [here](https://www.nyu.edu/life/information-technology/research-computing-services/high-performance-computing/high-performance-computing-nyu-it/hpc-accounts-and-eligibility.html). If you CS advisor is not listed in the sponsor list, you can use Prof. Bello (jb2843). Once this approved, you'll be able to `ssh` into the cluster. Here are some general recommendations/tips based on what has worked for me in the past:

## General tips 
- `ssh`-ing into the cluster: 
`ssh <NetID>@log-1.hpc.nyu.edu` (you can also use `log-1` or `log-2`, or `greene`)
- moving files from you local to HPC: `scp -r <myFolder> <NetID>@log-1.hpc.nyu.edu:/home/<NetID>/scratch` (this example uses `scratch`, but you can read about the other storage options [here](https://sites.google.com/nyu.edu/nyu-hpc/hpc-systems/hpc-storage/data-management).
- You can connect an IDE such as VSCode to remote access as well - I use the Remote - SSH and Remote Explorer extensions. You can ssh in through the IDE and then access your code and actively update it on the server there.
- You can set up a directory on HPC as a remote for a local **Github** repository. This can be a bit complex and people have a lot of different workflows for this, but here is what works for me: 
    1. Clone your repository to your local
    2. Use the command above to `scp` that directory onto HPC
    3. Back inside your local repo, add this as a remote, e.g. `git remote add <remoteName> <netID>@log-1.hpc.nyu.edu:/pathToDirectory`
    4. While working on the remote HPC copy, you can `git add` and `git commit`, but not push directly to the cloud. So what I do is make changes on the remote, add/commit anything I need, then go back to the local version, run `git pull <remoteName> main` which pulls the remote changes down to local, and finally push these changes to the cloud: `git push origin main` or whichever branch you're working on.
- **Requesting GPU**: `srun --cpus-per-task=12 --mem=32GB --gres=gpu --time=6:00:00 --pty /bin/bash` this would request one GPU for 6 hours, of any GPU type.
- If you have been allocated a GPU, you can run multiple interactive sessions on that GPU. For example, if I have been allocated `gr040`, then I can open a new terminal, `ssh` in, and then run `ssh gr040` to now get onto that GPU node and run another job
    - Use `nvidia-smi` to check your GPU utilization! 
    - ⚠️ A common issue is GPU under-utilization. You will get many emails from HPC about this, and they can kill jobs if your average 2 hour utilization is under some threshold :')
- Use `tmux` on HPC to persist your interactive sessions!

## Getting started with a Python environment on HPC
There are many ways to do this, and the main HPC docs provide a different route. But here's my flow I've found quite easy - a nice way to manage Conda environments with singularity.

Use **singuconda**! Follow the installation instructions [here](https://github.com/beasteers/singuconda?tab=readme-ov-file#Install) which are very clear for installing singuconda.

Once you have followed the steps below, inside your working directory on HPC, you'll have `sing` and `singrw`. For installing python packages into this conda environment/using pip, use `singrw`. To activate this environment, run `./singrw`, then go ahead and install there. If you are not installing packages (e.g. most of the time, when you're running interactive jobs), use `./sing`. 

General workflow: 
1. `ssh` to HPC
2. `tmux` to persist your terminal window (e.g. if you close your laptop, your interactive job will keep running!)
3. request GPU (see above)
4. navigate to working directory with `singuconda` installed
5. run `./sing` to start the interactive session
6. run python code directly here, e.g. `python myscript.py`. 
7. 🪄 that's it! 🪄

You can also run `sbatch` jobs per the HPC docs but I've found this workflow above to be totally sufficient for training large scale models even over multiple days.

Good luck!
