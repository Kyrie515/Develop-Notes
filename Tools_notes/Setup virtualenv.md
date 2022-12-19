## Setup `virtualenv` and `virtualenvwrapper` on Ubuntu

`virtualenv` is a python virtual environment where users could install different versions of modules according to the situation.And `virtualenvwrapper` is a manager which make the commands such as `mkvirtualenv` `workon` `rmvirtualenv` in the PATH of the Ubuntu.

#### 1.Install virtualenv and virtualenvwrapper

```shell
pip3 install virtualenv

pip3 install virtualenvwrapper
```

We are going to modify your .bashrc file by adding a row that will adjust every new virtual environment to use Python 3. We will point virtual environments to the directory we created above (.virtualenv) and we will also point to the locations of the virtualenv and virtualenvwrapper.

#### 2. Modify the .bashrc file

```bash
vim ~/.bashrc
```

Add following lines to the opening file

```bash
#Virtualenvwrapper settings:
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_VIRTUALENV=/home/user_of_yourself/.local/bin/virtualenv
source ~/.local/bin/virtualenvwrapper.sh
```

And do

```bash
source .bashrc
```

to make the file into effort

#### 3. Use the virtualenv

we could make a new virtual environment by the following command

```bash
mkvirtualenv name_of_your_virtualenv
```

and also we could use the following command to enter into the virtualenv we create before

```bash
workon name_of_your_virtualenv
```

