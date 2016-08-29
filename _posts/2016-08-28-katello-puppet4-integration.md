---
layout: post
title: Katello Puppet4 integration
date: 2016-09-19
author: Zach Huntington-Meath
tags:
- katello
- puppet 4
excerpt: |
    Puppet 4 integration is coming to Katello and we wanted to share with you
    how it works!
---

### Puppet 4 Integration with Katello

Recently Foreman has grown to work with Puppet 4. Katello is following suite and will soon be ready to use Puppet 4. In this post we will explain how to create new Katello environments Puppet 4 ready, and upgrade exisiting systems.

There are some important things to know about environments with Katello and Puppet 4:

Puppet 4 uses a Java Virtual Machine and requires more memory than Puppet 3 had before. This means that systems acting as Katello servers with Puppet 4 need a larger amount of memory. At a minimum we are requiring 8 GB of memory, and reccomending 16GB (<-- need a fact check on this). 

Puppet 4 masters can still provision computers with Puppet 3 with [some configuration](https://docs.puppet.com/puppetserver/latest/compatibility_with_puppet_agent.html). This can help with systems that are migrating over to Puppet 4. You can start with upgrading your masters to Puppet 4 and then gradually upgrading you agents.

You can use this matrix as a quick reference of what can work with what:

| | Puppet 3 client | Puppet 4 client
| :--- | :---: | :---: 
| **Puppet 4 server** | Compatible | Compatible
| **Puppet 3 server** | Compatible | Not compatible

### How to install Katello with Puppet 4

#### Production Environments
If you wanted to create a new Katello server configured with Puppet 4 the easiest way to do this would be to spin up a Virtual Machine with [Forklift](https://github.com/Katello/forklift).

To do this you can follow these instructions:

```bash
git clone https://github.com/katello/forklift.git
cd forklift
vagrant up centos7-katello-p4
```

Then if you want to bring up a capsule quickly you can run:

```bash
vagrant up centos8-capsule-p4
```

Once this is done you'll have a configured capsule to go along with your Katello server.

### Details on how to upgrade both Katello servers, capsules, and hosts to use puppet 4

We have a [great video](https://youtu.be/GFNPHypFhl4?t=2545) and [excellent documentation](http://www.katello.org/docs/nightly/upgrade/puppet.html) on how to upgrade from Puppet 3 to 4.

To upgrade an exisisting Katello instance to Puppet 4 it is straight forward:

Before starting backup or take snapshot the Katello server.

Then run

```bash
katello-service stop # This will stop all services.
foreman-installer --upgrade-puppet # This performs the upgrade.
```

The --upgrade-puppet command will restart the services, but you can make sure they are running with `hammer ping`. If you run puppet --version on the server, it should show `4.6.0` or a smilar 

#### Development environment

If you are so inclined you can create a development environment of Katello using Puppet 4. To do this you can spin it up (also with Forklift) as a virtual machine with the following configuration:

```yaml
example-dev-p4:
  box: centos7
  memory: 9000
  shell: 'yum -y install ruby && cd /vagrant && ./setup.rb'
  options: --scenario=katello-devel --puppet-four
  installer: --katello-devel-github-username <YOUR GITHUB USERNAME> --disable-system-checks
  ansible:
    group: 'server'
```
* Note this setup does require that you have a fork of Katello setup on your github account.

Then `vagrant up example-dev-p4` and let it spin up the box.


#### Extra configuration

Recently we added a webpack to foreman allowing new forms of Javascript to be used with our frontend. To get this working properly there are a few extra steps:

```bash
vagrant ssh example-dev-p4
cd foreman
sudo yum install npm
npm install
rails s
```

If you'd like you can also disable the webpack after doing the steps above.

In the development config file in foreman under `config/environments/development.conf` you'll this setting:

```php
config.webpack.dev_server.enabled = false # Replace the setting to false
```

then run

```bash
rake webpack:compile
```

With that you'll be able to run your dev server without having to do anything else with webpack.

#### Making Capsules

Then if you wanted to create a capsule with Forklift you can follow these steps:

* In your boxes.yml file you can write


```yaml
example-capsule-p4:
  box: centos7
  ansible:
    playbook: 'playbooks/capsule-dev.yml'
    options: --puppet-four
    group: 'capsule'
    server: '<name of your katello box>'
```


* After run:

```bash
vagrant up example-capsule-p4
```

### Contributors

I would like to thank everyone on the Katello team for putting in the effort for making this happen. As well as Foreman, the community  that puts so much effort into these great projects!

### More Information

Here are some good places to find more information about Puppet4 and using it with Katello.

[Puppet 4 Documentation](https://docs.puppet.com/puppet/4.5/reference/)

[Katello Puppet 4 Documentation](http://www.katello.org/docs/nightly/upgrade/puppet.html)

[Katello repository](https://github.com/Katello/katello)

[Forklift repository](https://github.com/Katello/forklift)
