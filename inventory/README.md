## Working with inventory files

> **An inventory file is the source of truth for Ansible (there is also an advanced concept called dynamic inventory, which we will cover later).**

> **It follows the INI format and tells Ansible whether the remote host or hosts provided by the user are genuine or not.**

> **`An INI file is a configuration file for computer software that consists of a text-based content with a structure and syntax comprising key-value pairs for properties, and sections that organize the properties.`**

> **Ansible can run its tasks against multiple hosts in parallel. To do this, you can directly pass the list of hosts to Ansible using an inventory file.**

> **Ansible allows you to group your hosts in the inventory file; the file passes the group name to Ansible. Ansible will search for that group in the inventory file and run its tasks against all the hosts listed in that group.**

> **You can pass the inventory file to Ansible using the `-i` or `--inventory-file` option followed by the path to the file.**

> **If you do not explicitly specify any inventory file to Ansible, it will take the default path from the `host_file` parameter of `ansible.cfg`, which defaults to `/etc/ansible/hosts`**

## The basic inventory file

##### Before diving into the concept, first let's look at a basic inventory file in the following screenshot:

```
$ cat hosts
www.example.com
web001
db005
192.168.198.146
```

##### Ansible can take either a hostname or an IP address within the inventory file. 

##### In the preceding example, we specified four servers; Ansible will take these servers and search for the hostname that you provided, to run its tasks. 

##### If you want to run your Ansible tasks against all of these hosts, then you can pass `all` to the hosts parameter while running the `ansible-playbook` or to the `ansible` command; this will make Ansible run its tasks against all the hosts listed in an inventory file.

##### The command that you would run is shown in the following screenshot:

```
ansible all -i hosts -m ping
```

##### The above command will spawn off four parallel processes, one for each machine. The default number of parallel threads is five. For a larger number of hosts, you can increase the number of parallel processes with the `-f` or `--forks=< value >` option.

`Coming back to the features of the file, one of the drawbacks with this type of simple inventory file is that you cannot run your ansible tasks against a subset of the `hosts`, that is, if you want to run ansible against two of the hosts, then you can't do that with above shown inventory file`


##### To deal with such a situation, Ansible provides a way to `group` your hosts and run Ansible against that group.

## Groups in an inventory file

##### In the following example, we grouped the inventory file into three groups, that is, `app`, `db`, and `jump`:

```
$ cat hosts
[app]
www.example.com
web001

[db]
db005

[jump]
192.168.198.146
```

##### Now, instead of running `ansible` against all the hosts, you can run it against a set of hosts by passing the `group name` to the ansible command. 

##### When `ansible` runs its tasks against a `group`, it will take all the hosts that fall under that group. 

##### to run Ansible against the `app` group, you need to run the command line as below:

```
ansible app -i hosts -m ping 
```

##### with above command, ansible will run its tasks against the hosts `example.com` and `web001`.

##### You can still run Ansible against a single host by directly passing the `hostname` or against all the hosts by passing `all` to them.

## Groups of groups

##### Grouping is a good way to run Ansible on multiple hosts together. Ansible provides a way to further group multiple groups together. 

##### For example, let's say, you have multiple application and `database` servers running in the `east coast` and these are grouped as application and db. 

##### You can then create a master group called `eastcoast`. Using this command, you can run Ansible on your entire `eastcoast` data center instead of running it on all groups one by one.

##### Let's take a look at an example shown below 

```
$ cat hosts
[app]
www.example.com
web001

[db]
db005

[jump]
192.168.198.146

[eastcoast:children]
app
db
```

## Regular expressions with an inventory file

##### An inventory file would be very helpful if you have many servers. Let's say you have a large number of web servers that follow the same naming convention, 

##### for example, `web001`, `web002`, ???, `web00N`, and so on. Listing all these servers separately will result in a dirty inventory file, which would be difficult to manage with hundreds to thousands of lines. 

##### To deal with such situations, Ansible allows you to use `regex` inside its inventory file. The following shows an example of multiple servers:

```
$ cat hosts
[app]
www.example.com
web[001:200]

[db]
db[001:020]

[jump]
192.168.2.[1:3]

```

From the preceding example, we can deduce the following:

![inv-regex](https://github.com/lerndevops/ansible/blob/master/static/inv-regex.PNG)
