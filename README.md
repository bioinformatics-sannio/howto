## Computational Infrastructure available at DST Unisannio

- Superdome server (224 CPU cores Intel(R) Xeon(R) Platinum 8280 CPU @ 2.70GHz, 1.5 TB RAM, 1 GPU NVIDIA Tesla V100)

In this document some technical information to access the computational infrastrucutre.

## Setup miniconda local notebook with python, pytorch, R

Get the last version of miniconda at https://repo.anaconda.com and install it in your local home.

```console
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh 
```

Update Conda

```console
conda update -c conda-forge conda
```

Install pytorch with cuda enabled and other packages such as `biopython`

```console
conda install -c conda-forge pytorch torchvision torchsummary
conda install -c conda-forge biopython matplotlib scikit-learn pandas seaborn
pip install pkbar torchsummary
```

Install R and R essentials. Set the appropriate version if needed.

```console
conda install -c conda-forge r-base=4.1
conda install -c conda-forge r-essentials boost
```

Install Bioconductors and other useful R packages.

```console
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")

BiocManager::install("DECIPHER")

install.packages("kit")
install.packages("usedist")
install.packages("fastcluster")
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

If the port number (e.g. 8899) is not available choose a different one. The server outputs the link to connect with a token. Copy in the clipboard and detach the session with `Ctrl-b d`.

Useful commands to manage a tmux session (more info available here https://github.com/tmux/tmux/wiki/Getting-Started).

```console
tmux ls                  # to see if there are any other tmux sessions
tmux a                   # attach to the most recent session that was created
tmux new -s "Jupiter"    # launch a new session names with -s
tmux attach -t "Jupiter" # Connect to a sessione named with -s
```

In your local shell (client side) set an ssh tunnel and connect to the notebook with http://localhost:8899
The port should be the same chose for the notebook server. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8899:localhost:8899 <userid>@<ip> -p <sshport>
```

## Setup a local rootless podman container with R and rstudio server
 
If you want to share a volume (local dir) with the container create in your local home a directory (e.g. rstudioshare) and set the appropriate permissions with `podman unshare` command (1000:1000 are the uid and gid of container's rstudio user).

```console
mkdir rstudioshare
podman unshare chown 1000:1000 -R $HOME/rstudioshare
```

From your local account the shared volume is readonly while from the container it have read and write permissions. You can gain the original local user permissions with `podman unshare`command.
 
```console
podman unshare chown root.root -R $HOME/rstudioshare
```

Run the container in detached mode. Choose the appropriate port number (e.g. 8787) if not available and a password.
 
```console
podman run -dt -p 8787:8787 -v $(pwd)/rstudioshare:/home/rstudio/hosthome:Z --name rstudio -e PASSWORD=<apassword> rocker/rstudio
```
 
In your local shell (client side) set an ssh tunnel and connect to rstudio server with http://localhost:8787 and login with rstudio userid.
The port must be the same used to run the container. Information about <userid> <ip> and <sshport> are the same you adopt for ssh login.

```console
ssh -N -f -L localhost:8787:localhost:8787 <userid>@<ip> -p <sshport>
```
 
Some useful podman commands to manage containers. `Container ID` can be the container name defined with the --name parameter.

```console
podman ps -a.                             # list running containers
podman stop <Container ID>                # stops the execution of a container
podman rm <Container ID>                  # remove a stopped container
podman exec -it <Container ID> /bin/bash. # connect to a running container shell. <Container ID> can be obtained with podman ps -a
podman commit <Container ID>.             # commit changes in a running container (e.g. installed packages).
```
