# Welcome to Peter's guide for IT

## Docker

### Docker registry HTTP API v2
**Authentication**
`token=$(curl -s -X POST -H "Content-Type: application/json" -d '{"username": "'${USERNAME}'", "password": "'${PASSWORD}'" http://hub.docker.com/v2/users/login/ | jq -r .token)`

**List tags of a public image**
`curl -L https://registry.hub.docker.com/v2/repositories/${namespace}/${image}/tags?page_size=1024 | jq ".results[].name"`
for instance:
`curl -L https://registry.hub.docker.com/v2/repositories/swaggerapi/swagger-ui/tags\?page_size\=1024 | jq ".results[].name"` 

## jq
jq is a command line tool to process json format.
-r	use to return the value of a key without the surronding quotes

https://docs.docker.com/registry/spec/api/

## Mysql

### MySql SH Mac OS
Connect to the mysql server running in localhost
`mysqlsh --sql --database mydatabase --user root --port 3306`


## PowerShell

Set execution policy
Set-ExecutionPolicy RemoteSigned

## Ansible
Magic variables in ansible:
 - `hostvars`
 - `groups`
 - `group_names`
 - `inventory_hostname`
`hostvars` let you access variables for another host
