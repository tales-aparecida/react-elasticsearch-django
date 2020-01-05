# React-ElasticSearch-Django

As the name implies, this projects aims to showcase some useful and easy to
setup technologies. The resulting product should be a React powered webpage to
search the objects managed by your own Django server.

If you have any problem during this guide add an issue or open a Pull Request
to fix it! :)

## Installation

This project is beginner friendly and tries to cover all steps needed during
the setup of your first project. If you fell confident, just skip to section 5,
where the requirements will be ready.

### Git Setup

First, if you don't have a _ssh key_ yet, run `ssh-keygen`. This will create a
RSA-2048 key pair by default (other algorithms are available), which you can
optionally set a password (setting a password is recommended, but can be
triggering if you `git push` too often). This key will replace your password
during online Git operations, like `fetch`, `pull` and `push`.

Then, open GitHub settings, go to **SSH keys** and add the public part of your
key, you can get it with `cat ~/.ssh/id_rsa.pub | xclip -sel clip`, if xclip is
installed, or just `cat ~/.ssh/id_rsa.pub` and copy with _Ctrl+Shift+C_, as
usual.

Now, go to your desired path and run the command below to download the repo.

TODO: Add fork instructions

```sh
git@github.com:thalelinh/react-elasticsearch-django.git
```

### Python dependencies

First, make sure you have **Python 3** and **pip** installed with
`pip3 --version`, and updated with `pip3 install --upgrade setuptools pip`.

#### Python Virtualenv (recommended)

`virtualenv` is a tool to create isolated environments in Python.

Install a Python virtual environment handler, we recommend using
[virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/).

```sh
pip3 install virtualenv # alternatively `python3 -m pip install...`
```

```sh
pip install virtualenvwrapper # apparently you must use python2 for this step
```

Add the following at the bottom of your `~/.bashrc`

```sh
# Python 3 modules
USER_BIN=$HOME/.local/bin

# Virtualenvs
export WORKON_HOME=$HOME/.virtualenvs
[[ ! -d $WORKON_HOME ]] && mkdir -p $WORKON_HOME && echo $WORKON_HOME "created"
source $USER_BIN/virtualenvwrapper.sh

# Path config
PATH=$PATH:$USER_BIN
export PATH
```

Now `source ~/.bashrc` or just open a new terminal window to enable the
virtualenv commands.

```sh
# Creates an environment named 'myproj', it will use a specific python version
# given the -p flag
mkvirtualenv -p $(which python3) myproj 
workon myproj # Activates the 'myproj' virtualenv
```

To disable an environment use `deactivate`. To remove it use
`rmvirtualenv VIRTUALENV_NAME`

If you have this error when you create environment: **ModuleNotFoundError: No
module named 'distutils.spawn'**, try to `sudo apt install python3-distutils`.

#### Install dependencies

Install all requirements, if you've set up virtualenv, create an _environment_
and activate it first:

```sh
pip install -r requirements.txt # Install dependencies listed on requirements.txt
```

### Docker and Docker-compose

As a way facilitate database deployment as well as the ElasticSearch engine, is
to install docker now.

#### [Docker](https://linuxize.com/post/how-to-install-and-use-docker-on-ubuntu-18-04/)

> Docker is a containerization technology that allows you to quickly build,
test and deploy applications as portable, self-sufficient containers that
can virtually run everywhere.

_The following instructions were tested on Ubuntu 18.04. The lines end with
`&&\` to allow for copy & paste_

```sh
# Add dependencies
sudo apt install apt-transport-https ca-certificates curl software-properties-common && \

# Import the docker repository GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \

# Add the Docker APT repository
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \

# Update repositories list
sudo apt update && \

# Install docker
sudo apt install docker-ce
```

The **Docker** service should start automatically, to check you can run: 

```sh
sudo systemctl status docker
```

Which should output something like:

```
 docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-03-21 12:07:22 -03; 30s ago
     Docs: https://docs.docker.com
 Main PID: 15538 (dockerd)
    Tasks: 16
   CGroup: /system.slice/docker.service
           └─15538 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```

_Obs.: This usually increases your boot time in around 30s, if you care about
that run `sudo systemctl disable docker` to disable auto-start and `sudo
systemctl start docker` to start manually._

By default Docker requires administrator privileges. If you want to run Docker
commands as a standard user, i.e. without prepending `sudo`, you'll need to add
your user to the docker group which is created during the installation of the
Docker CE package. You can do that by typing:

```sh
sudo usermod -aG docker $USER
```

**Log out and log back in** so that the group membership is refreshed.

To verify that Docker has been successfully installed and that you can run
docker commands without prepending sudo, type the following command which will
download a test image, run it in a container, print a “Hello from Docker”
message and exit:

```sh
docker container run hello-world
```

Which should result on:

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete 
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```

#### [Docker-compose](https://linuxize.com/post/how-to-install-and-use-docker-compose-on-ubuntu-18-04/)

> Docker Compose is a tool that allows you to define and manage multi-container
Docker applications. It uses a YAML file to configure the application’s
services, networks and volumes.

```sh
# Download the binary into `/usr/local/bin`
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
# Grant executable permission to the Compose binary
sudo chmod +x /usr/local/bin/docker-compose
```

To check if the installation was successful run:

``` docker-compose --version ```


### Database

Prepare the database and start the Django server, first you need to use:

```sh
# Create database
python manage.py makemigrations && python manage.py migrate
```

With the first command `makemigrations` you’re telling Django that you’ve made
some changes to your models and that you’d like the changes to be stored as a
migration, since you haven't made any changes nothing should happen.

The command `migrate` looks at the INSTALLED_APPS setting and creates any
necessary database tables according to the database settings in your
settings.py file and the database migrations shipped with the app. You can see
more in [Django Tutorial](https://docs.djangoproject.com/en/2.2/intro/tutorial02/).

Now you can create a super user and run server:

```sh # Create the Eliot admin user python manage.py createsuperuser # Start
the server. Open localhost:8000 on your browser python manage.py runserver ```

When you run server, put the same login/password used in **createsuperuser**.
If you wander a bit into the system you may face some missing components.  You
are missing React. Follow through the next session.


### JS dependencies

#### NVM (Recommended)

To avoid NodeJS versioning problems install the [Node Version
Manager](https://github.com/nvm-sh/nvm)

To **install** or **update** nvm, you should run the install script. To do
that, you may either download and run the script manually, or use the following
cURL or Wget command:

```sh curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh
| bash ```

```sh wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh
| bash ```

Running either of the above commands downloads a script and runs it. The script
clones the nvm repository to `~/.nvm`, and adds the source lines from the
snippet below to your profile (`~/.bash_profile`, `~/.zshrc`, `~/.profile`, or
        `~/.bashrc` (**most likely**) ).


```sh export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s
"${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")" [ -s "$NVM_DIR/nvm.sh" ]
&& \.  "$NVM_DIR/nvm.sh" # This loads nvm ```

Now reset your terminal or `source ~/.bashrc`.

#### Node 10 and NPM

To install **Node 10** along with `npm` run:

```sh nvm install 10 ```

Yeah, as simple as that. Check with `node -v` and `npm -v`.

To install JS dependencies run `npm install` while on the root folder.




### Database

There are many possibilities, each with its own features. In this project I
will be using Postgres, but be free to try to venture out of the rails. :)

#### SQLite

Simple database manager that stores everything in a simple file.

#### MySQL/MariaDB

Great full-featured mainstream database, its setup can be a little troublesome,
so I would suggest using a Docker container if you want to try it first, it is
as easy as running:

```sh
docker run \
           --name mysql \                       # Container name
           -e MYSQL_ROOT_HOST=% \               # '%' is a 'localhost' alias
           -e MYSQL_ROOT_PASSWORD=secret \      # Password for user 'root'   
           -d mysql                             # Image name at dockerhub 
```

If you want more options, go to its [dockerhub page](https://hub.docker.com/_/mysql/)

The command above should be run only once, to restart the container in the
future use `docker start mysql`.

You must wait for the container to be fully online, you can check its progress
with `docker logs -f mysql`. You can also check if it's possible to connect
using a MySQL client. To install it and start a connection, run:

```sh
sudo apt install libmysqlclient-dev mysql-client-core-5.7
mysql -h127.0.0.1 \ # Host address, if using '-h localhost' use '--protocol tcp' too
          -P3306 \ # Host port, default is 3306
          -uroot \ # User to connect
          -psecret # Password for the given user, don't add spaces between flag and value
```

If you run the server before it's ready, you might face this error: 

```
ERROR 2013 (HY000): Lost connection to MySQL server at 'reading initial
communication packet', system error: 2
```
    
If so, just wait for it to come online. Now, if you have the following error
make sure you container is up: 

```
ERROR 2003 (HY000): Can't connect to MySQL server on '127.0.0.1' (111)
```


## Running

### Webpack

> Webpack is an open-source JavaScript module bundler. Webpack takes modules
> with dependencies and generates static assets representing those modules. It
> takes the dependencies and generates a dependency graph allowing web
> developers to use a modular approach for their web application development
> purposes. The bundler can be used from the command line, or can be configured
> using a config file which is named webpack.config.js - **Wikipedia**

We use webpack to _bundle_ some React components that are inserted into Django
templates allowing for our incremental frontend refactoring. You don't need to
know deeply how it works, only that you can bundle once running `npm run
webpack`, which will generate an updated
**myproj/case_analysis/static/case_analysis/dist/main.js** as well as a
**myproj/webpack-status.json**. The former contains all React components, whilst
the latter contains the results of the last bundling. Neither are _pushed_ into
production, they are bundled as part of the deployment pipeline.

Now, if you are developing a **React** component you can run `npm start`. This
is an alias that starts a webpack watcher, which will update main.js
dynamically, and the Django server, i.e. it is all that you need. In this case
you will need to set the `MEDIA_ROOT` constant in your **settings.py**, which
will require cloning **settings_develop.py** instead of linkint to it.

## Coding

### Coding environment

First, make sure to `pip install -r requirements_dev.txt`, to enable Python
linting and auto format.

We recommend using **VScode** with the following extensions and
`settings.json`, you can search for these extensions in the extension browser
or `Ctrl+P`, paste the command between paretheses and hit `Enter`.

- Python (`ext install ms-python.python`)
- Django (`ext install batisteo.vscode-django`)
- GitLens (`ext install eamodio.gitlens`)
- ESLint (`ext install dbaeumer.vscode-eslint`)
- Prettier (`ext install esbenp.prettier-vscode`)


Now, edit `.vscode/settings.json` or press `Ctrl+Shift+P` and search for _"open
folder settings"_ then paste the snippet:

```json { // Eliot config for `.vscode/settings.json` "python.venvPath":
    "~/.virtualenvs", // "python.pythonPath":
        "~/.virtualenvs/myproj/bin/python",  // Update this with your virtualenv
        path and uncomment "python.linting.enabled": true,
        "python.linting.flake8Enabled": true,
        "python.linting.flake8CategorySeverity.E": "Information",
        "python.linting.pylintEnabled": true, "python.formatting.provider":
            "autopep8", "[python]": { "editor.formatOnSave": true,
                "editor.formatOnPaste": true, "editor.formatOnSaveTimeout":
                    5000, "editor.rulers": [80], // "editor.codeActionsOnSave":
                { "source.organizeImports": true, "source.fixAll": true }, //
                buggy }, "files.watcherExclude": { "**/myproj/documents/**":
                    true, "nm_search/docs/*": true, "node_modules": true, },

  "eslint.autoFixOnSave": true, "eslint.enable": true, "[javascript]": {
      "editor.formatOnSave": true, "editor.rulers": [80],
      "editor.formatOnSaveTimeout": 5000, }, } ```

A keybind suggestion to add to your VScode that will allow you to go navigate
back when browsing files with Ctrl+- and forward with Ctrl+Shift+-. To edit
keybindings.json press Ctrl+Shift+P, which will open a shortcut input, then
type "Open keyboard shortcuts (JSON)":

``` [ { "key": "ctrl+=", "command": "workbench.action.zoomOut" }, { "key":
    "ctrl+-", "command": "-workbench.action.zoomOut" }, { "key": "ctrl+=",
        "command": "-workbench.action.zoomIn" }, { "key": "ctrl+-", "command":
            "workbench.action.navigateBack" }, ] ```

#### Useful shortcuts

- Ctrl+- : Navigate back (Not default)
- Ctrl+Shift+- : Navigate forward
- Ctrl+P : Navigate to file
- Ctrl+Shift+P : Shortcut prompt
- Ctrl+t : Navigate to Object (Needs ctags installed. VSCode will prompt if you
        don't have it)
- Ctrl+k Ctrl+1..9: Fold the code at the Nth scope
- Ctrl+Shift+F : Find in all files (You can filter which files you want to
        search)


### First commit

For your first commit, you need to create or change branch:

```sh git checkout EXISTING_BRANCH_NAME # Go to branch git checkout -b
NEW_BRANCH_NAME # Creates new branch ```

After that you will work on your task, thereafter you will save your changes
and run:

```sh git add NAME_FILE_CHANGE git commit -m "I changed that..." git push ```

If the branch not exists, you need to run that:

```sh git push --set-upstream origin NAME_BRANCH ```

After that, you gonna visit BitBucket and go to Branch, choose your branch,
      Create Pull Request, and send your branch to PR.

All this steps can be done inside VSCode clicking some buttons.


# Random Guides

This section should be just links to confluence, but in the mean time...

## Plot Database

Install **graphviz** and **pygraphviz** with 

``` sudo apt install graphviz pip install pygraphviz ```

Now, to plot the graphs to a file do:

``` # Save the DB diagram to 'diagram.png' excluding models from #
'django-simple-history'. Replace [APPS] by a list of the desired app names pym
graph_models --output diagram.png --exclude-models Historical* [APPS] ```

_(Try to install `libgraphviz-dev` if any errors occur.)_


## Celery

To start a worker and the scheduler:

```sh celery -A myproj worker -l info -B ```

To clear the queue:

```sh celery -A myproj purge ```

### Adding new tasks to celery Any new task must use the decorator `@task`. For
example:

``` from myproj.celery import app

@app.task def task_example(): pass ```

The new task must have a queue assigned to it. By default rules are specified
on `myproj/myproj/settings_modules/celery.py`. Tasks are routed to a queue
according to the dictionary `CELERY_TASK_ROUTES`.

The following entry routes every task under the module
`automation_evaluation.tasks` to the queue `PERIODIC_QUEUE`. 

``` CELERY_TASK_ROUTES = { 'automation_evaluation.tasks.*': {'queue':
    PERIODIC_QUEUE}, } ```

If a new module is added it must be included in this dictionary and usually
into one of the existing queues:

* Periodic Queue: long sporadic periodic tasks
* Priority Queue: tasks that must be run as soon as possible
* Automation Queue: tasks that get blocked by calling automation endpoints.

If a new queue is created, it must be included in the celery config
(`config/celery*.conf`) where workers are specified (`CELERYD_OPTS` entry),
    otherwise no worker will be listening to the queue. Because the actual
    configuration is stored in `/etc/conf.d`, the new configuration must be
    copied to the directory replacing the old configuration in there. Celery
    must be restarted.
