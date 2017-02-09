# Python@Fermilab

Short guide on how to get a nice scientific python environment working quickly with Python, ROOT, PyROOT, notebooks, etc.

[CERN Root Conda](https://nlesc.gitbooks.io/cern-root-conda-recipes/content/using_the_conda_binary_packages.html)

It pretty much goes like:

* Download Anaconda from here: [Link](https://www.continuum.io/downloads.) For gpvm machines you can just do:
 
```
wget "https://repo.continuum.io/archive/Anaconda2-4.2.0-Linux-x86_64.sh" -O anaconda.sh
chmod +x anaconda.sh
./anaconda.sh
```

* Make sure your PATH environment has your Anaconda bin directory in it, this can be a useful alias to you when you login:

```
alias soconda='export PATH=/uboone/app/users/YOURUSERNAME/Anaconda2/bin:${PATH}'
```

* Add the Conda NLeSC channel to Conda:

```
conda config --add channels https://conda.anaconda.org/NLeSC
```

* Create your nice environment:

```
conda create --name=py2 root=6 python=2
```

* RootNumpy is also useful in this case (but must be installed outside the environment):

```
conda install -f root-numpy
```

* Done! Anytime you need your new virtual environment, all you need to do is:

```
soconda
source activate py2
```