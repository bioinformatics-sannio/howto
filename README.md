## Computational Infrastructure available at DST Unisannio

- Superdome server (224 CPU cores Intel(R) Xeon(R) Platinum 8280 CPU @ 2.70GHz, 1.5 TB RAM, 1 GPU NVIDIA Tesla V100)

In this document some technical information to access the computational infrastrucutre.

## Setup miniconda local notebook with python, pytorch, R

Get the last version of miniconda at https://repo.anaconda.com and install it in your local home.

```console
wget https://repo.anaconda.com/miniconda/Miniconda3-py39_4.9.2-Linux-x86_64.sh
bash Miniconda3-py39_4.9.2-Linux-x86_64.sh 
```

Update Conda

```console
conda update -n base -c defaults conda
```

Install pytorch with cuda enabled

```console
conda install pytorch torchvision -c pytorch
conda install -c conda-forge biopython
```

Install R and R essentials. Set the appropriate version if needed.

```console
conda install -c conda-forge r-base=4.0.3
conda install -c conda-forge r-essentials
```

Install Jupiter notebook

```console
conda install -c conda-forge notebook
```

Lauch the notebook server in a tmux session. 

```console
tmux new -s "Jupiter"
jupyter notebook --no-browser --port=8899
```

If the port number 8899 not available choose a different one. The server gives the link to connect with a tocket. Copy in the clipboard and exit the session with Ctrl-b d.

Some useful commands to manage a tmux session. More info about tmux available here https://github.com/tmux/tmux/wiki/Getting-Started.

```console
tmux ls                  # to see if there are any other tmux sessions
tmux a                   # this will connect to the most recent session that was created
tmux new -s "Jupiter"    # launch a new session names with -s
tmux attach -t "Jupiter" # Connect to a sessione named with -s
```

In your local shell (client side) set an ssh tunnel and connect to the notebook with http://localhost:8899
The port should be the same chose for the notebook server. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8899:localhost:8899 <userid>@<ip> -p <sshport>
```

## Setup a local rootless podman container with R and rstudio server
 
If you want to share a volume (local dir) with the container then create in your local home a directory and set the appropriate permissions with podman unshare. 1000:1000 are the uid and gid of container rstudio user.

```console
mkdir rstudioshare
podman unshare chown 1000:1000 -R $HOME/rstudioshare
```

From your local account the shared volume is readonly while from container it is read and write. You can gain the original local user permissions with podman unshare.
 
```console
podman unshare chown root.root -R $HOME/rstudioshare
```

 
Launch the container in detached mode. Choose the appropriate port number if not available and a password.
 
```console
podman run -dt -p 8787:8787 -v $(pwd)/rstudioshare:/home/rstudio/hosthome:Z --name rstudio -e PASSWORD=<apassword> rocker/rstudio
```
 
In your local shell (client side) set an ssh tunnel and connect to rstudio server with http://localhost:8787 and login with rstudio/<apassord>.
The port must be the same chose for the container. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8787:localhost:8787 <userid>@<ip> -p <sshport>
```
 
Some useful podman commands to manage containers.

```console
podman ps -a.                             # list running containers
podman stop <Container ID>                # stops the execution of a container
podman rm <Container ID>                  # removed a stopped container
podman exec -it <Container ID> /bin/bash. # connect with shell to a running container. <Container ID> can be obtained with podman ps -a
podman commit <Container ID>.             # commit changes in a running container (e.g. installed packages).
```
