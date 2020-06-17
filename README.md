# Welcome to Peter's guide for IT

## Docker

### Docker registry HTTP API v2
**Authentication**
`token=$(curl -s -X POST -H "Content-Type: application/json" -d '{"username": "'${USERNAME}'", "password": "'${PASSWORD}'" http://hub.docker.com/v2/users/login/ | jq -r .token)`

**List tags of a public image**
`curl -L https://registry.hub.docker.com/v2/repositories/${namespace}/${image}/tags?page_size=1024 | jq ".results[].name"`
for instance:
`curl -L https://registry.hub.docker.com/v2/repositories/swaggerapi/swagger-ui/tags\?page_size\=1024 | jq ".results[].name"` 

**Copy files from container to local**
docker cp cbf\_cbfdb\_1:/root/cbfapp.sql .

## jq
jq is a command line tool to process json format.
-r	use to return the value of a key without the surronding quotes

https://docs.docker.com/registry/spec/api/

## Mysql

### MySql SH Mac OS
Connect to the mysql server running in localhost
`mysqlsh --sql --database mydatabase --user root --port 3306`

exit with `\q`

**How to export a database**
mysqldump -u root -p mydatabase > db.sql

## PowerShell

Set execution policy
Set-ExecutionPolicy RemoteSigned

## Ansible
Control node requirements:
Currently Ansible can be run from any machine with Python 2 (version 2.7) or Python 3 (versions 3.5 and higher) installed.
This includes Red Hat, Debian, CentOS, macOS, any of the BSDs, and so on (technically a \*nix-like system)
Windows is not supported for the control node.

When choosing a control node, bear in mind that any management system benefits from being run near the machines being managed. If you are running Ansible in a cloud, consider running it from a machine inside that cloud. In most cases this will work better than on the open Internet.

Managed nodes requirements:

If you have SELinux enabled on remote nodes, you will also want to install
libselinux-python on them before using any copy/file/template related functions in Ansible. You
can use the yum module or dnf module in Ansible to install this package on remote systems
that do not have it.

By default, Ansible uses the Python interpreter located at /usr/bin/python to run its
modules.  However, some Linux distributions may only have a Python 3 interpreter installed to
/usr/bin/python3 by default.  On those systems, you may see an error like:
"module_stdout": "/bin/sh: /usr/bin/python: No such file or directory\r\n"

you can either set the ansible_python_interpreter inventory variable (see
How to build your inventory) to point at your interpreter or you can install a Python 2 interpreter for
modules to use. You will still need to set ansible_python_interpreter if the Python
2 interpreter is not installed to /usr/bin/python.

Ansible’s raw module, and the script module, do not depend
on a client side install of Python to run.  Technically, you can use Ansible to install a compatible
version of Python using the raw module, which then allows you to use everything else.
For example, if you need to bootstrap Python 2 onto a RHEL-based system, you can install it
as follows:
$ ansible myhost --become -m raw -a "yum install -y python2"

Magic variables in ansible:
 - `hostvars`
 - `groups`
 - `group_names`
 - `inventory_hostname`
`hostvars` let you access variables for another host

**include\_vars**

Loads YAML/JSON variables dynamically from a file or directory, recursively, during task runtime.
If loading a directory, the files are sorted alphabetically before being loaded.
This module is also supported for Windows targets.
To assign included variables to a different host than inventory_hostname, use delegate_to and set delegate_facts=yes.

**Local Fact**
If a remotely managed system has an /etc/ansible/facts.d directory, any files in this directory
ending in .fact, can be JSON, INI, or executable files returning JSON, and these can supply local facts in Ansible.
An alternate directory can be specified using the fact_path play directive.

**Difference between Dynamic and Static**
If you use any include\* Task (include_tasks, include_role, etc.), it will be dynamic.
If you use any import\* Task (import_playbook, import_tasks, etc.), it will be static.

The bare include task (which was used for both Task files and Playbook-level includes) is still available, however it is now considered deprecated.


## sed
sed is a stream text editor
TODO:
 - experiment with the sed output when passing a stream(audio, video, text live) to sed. Is it going to be on realtime?


#jinja2
from jinja2 import Environment, FileSystemLoader
import json

inventory = json.load(open('ansible_tower.json','r'))
env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('inputs.j2')

tags = inventory['software_components']
new_tags = {}
for software_component in inventory['software_components']:
    if software_component['name']  == "splunk":
        for tag in software_component['configuration']['tags']:
              new_tag = {tag['name']: tag['value']}
              new_tags.update(new_tag)

print(template.render(new_tags))
output_from_parsed_template = template.render(new_tags)
with open("my_new_file.html", "w") as fh:
    fh.write(output_from_parsed_template)
print(output_from_parsed_template)

## iterm2

**Pop up a notification**
`echo $'\e]9;Notification Text\007'`


# Sysadmin Linux

## Networking

###  DNS

If you want to know more about DNS these tools will help you 
- `nslookup`
- `host`
- `dig`

`nslookup` get the DNS servers used to reach a host
`host` use host to find the domain name of the dns server
`dig` can be use to query more details about a DNS server

Examples:

```bash
$ nslookup google.com
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	google.com
Address: 216.58.193.46

$ host  8.8.8.8
8.8.8.8.in-addr.arpa domain name pointer dns.google.

$ dig @8.8.8.8 youtube.com

; <<>> DiG 9.10.6 <<>> @8.8.8.8 youtube.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 38888
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;youtube.com.			IN	A

;; ANSWER SECTION:
youtube.com.		299	IN	A	216.58.193.14

;; Query time: 399 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Tue Mar 31 13:05:51 CST 2020
;; MSG SIZE  rcvd: 56

$  
```

## SSL

How to list a keystore certificates
openssl pkcs12 -nokeys -info -in p12/nonprod_webserver.p12 -passin pass:changeit

Creates a new keystore p12

openssl pkcs12 -export -in nonprod_webserver.pem -inkey nonprod_webserver.key -certfile nonprod_webserver.pem -name "examplecert" -out p12/nonprod_webserver.p12


## Get port listening for a processl
nestat -nltp number


## FInd tool
find log-clgw/ -type d -print0 | xargs -0 chmod 755
find log-clgw/ -type f -print0 | xargs -0 chmod 644

chown -R tomcat:tomcat log-clgw/

sed -i 's/umask 077/umask 022/' /home/jboss/.bashrc

grep umask /home/jboss/.bashrc

## Tomcat

**Load balancing**

A final note about setting activation to disabled:
The session id coming with a request is send either
as part of the request URL (;jsessionid=...) or via a cookie.
When using bookmarks or browsers that are running since a long time,
it is possible to send a request carrying an old and invalid session id
pointing at a disabled member.
Since the load balancer does not have a list of valid sessions, it will
forward the request to the disabled member. Thus draining takes longer than
expected. To handle such cases, you can add a Servlet filter to your web
application, which checks the request attribute JK_LB_ACTIVATION.
This attribute contains one of the strings "ACT", "DIS" or "STP". If you
detect "DIS" and the session for the request is no longer active, delete the
session cookie and redirect using a self-referential URL. The redirected
request will then no longer carry session information and thus the load
balancer will not send it to the disabled worker. The request attribute
JK_LB_ACTIVATION has been added in version 1.2.32.


## Tomcat

**Windows**
When running Apache Tomcat as a service on Windows, the JVM settings are stored in the Registry Editor at HKEY_LOCAL_MACHINE\SOFTWARE\Apache Software Foundation\Procrun 2.0\. This location contains a directory for either Tomcat 6, Tomcat 7 or Tomcat 8, which maintains a list of the parameters that can be configured. You can manually edit these values. However, this is not recommended.
