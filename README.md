Vagrant Learning
================

Using Vagrant is similar to using other CLI-based tools, such as
`git`. In your shell (I'm using PowerShell on the fancy Windows
Terminal; all these commands will also work with `bash`), make a new
directory for a Vagrant project:

```bash
# 
mkdir vagrant_learning
cd vagrant_learning
```

If you cloned this directory, you won't need to do this. Just `cd`
into this directory.

Now, we'll use `vagrant` to initialize our new directory with a
**box** (UNIX machine image).


```bash
# Init with 
vagrant init hashicorp/precise64
```

You can choose other boxes to `init` your project with from the
[Vagrant Cloud](https://app.vagrantup.com/boxes/search).

Run `vagrant up` to run your machine!

Working with your (virtual) machine
-----------------------------------

Now, you should be able to access your system by typing.

```bash
vagrant ssh
```

The files (from your host machine) should be available in the virtual
machine's `/vagrant/` directory. If you downloaded this git repository, 
you'll find a directory `ruby-scripts/` there. Try running `ruby foo.rb`. 
Wow! What a convoluted "Hello, World!" tutorial! 

If you make changes on your host
machine, you'll need to use `vagrant reload` to see those changes on
your virtual machine (if you're SSH'd into the virtual machine, you
can press `Ctrl + d` to exit).

### Adding more software, poking around

To install/try out things within your machine, you can use the logged
in `vagrant` account which has sudo privileges.

Just do stuff like `sudo apt-get install vim` or whatever to get the
tools you need.

### Halting, Suspending, Destroying

Some other commands you may find useful are `vagrant halt`, `vagrant
suspend` / `vagrant resume`, and `vagrant destroy`.

Doing `vagrant suspend` will save the state of your VM; running
`vagrant resume` allows you to pick up right where you left off.

`vagrant halt` stops the machine; you'll have to use `vagrant up` to
start the (same) machine up again.

Finally `vagrant destroy` will *destroy* the current VM. If you were
trying out stuff in SSH, using `sudo` to install new software, and so
on... these changes will all be lost with your machine. Running
`vagrant up` will create a new machine to replace the one you
destroyed.

### More info

Run `vagrant --help` to see what other commands are available.

Provisioning
------------

**Provisioning** is how you set up your system after loading some box
("image"), such as the official Ubuntu image provided by HashiCorp
(creators of Vagrant).

At the bottom of the `Vagrantfile` you will see a shell script for
doing stuff like installing new software. I followed the example setup
from
[here](https://github.com/addiscent/vagrant-ruby-rails/blob/master/Vagrantfile).

This code makes use of a Ruby
[Heredoc](https://www.rubyguides.com/2018/11/ruby-heredoc/) to "embed"
a shell script within a Ruby file (the Vagrant file uses Ruby's syntax).

```ruby
config.vm.provision "shell", inline: <<-SHELL, privileged: true

echo "--> I'm a script from the Vagrantfile."

# Try installing something
apt-get update

# Tools for working within the virtual machine
apt-get -y install vim tmux 
SHELL
```

### Spinning up an NGINX web server

NGINX is a small, quick web server. I looked at [this
guide](https://vegibit.com/how-to-provision-nginx-using-vagrant/) on
how to quickly set up an nginx server.

In the default Vagrantfile, uncomment out the line that looks like this.

```ruby
config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
```

This will allow port 80 on the guest system (the VM you're running
with Vagrant) to be accessible at localhost:8080 from your host
machine.

Now, go down to the provisioning Heredoc string we just made. Add in
these lines.

```bash
apt-get install -y nginx
service nginx start
```

Leaving all the default options, the `/usr/share/nginx/www` directory
will serve up static assets (like HTML files, CSS, etc.). 

If you try editing `/usr/share/nginx/www/index.html` you will see your
changes reflected at http://localhost:8080 on your host machine.

More Resources
--------------

Aside from the official docs, I looked at

- [Getting Started with Vagrant on Windows](https://www.sitepoint.com/getting-started-vagrant-windows/) (Zack Wallace, 01/12/2017)
