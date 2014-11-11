This is an [Ansible](http://www.ansible.com/home) role for a
reasonably secure installation of
[Marvel](http://www.elasticsearch.org/overview/marvel).  Marvel sits
on top of [ElasticSearch](http://www.elasticsearch.org/) and
[nginx](http://nginx.org) is used to provide a virtual server.

You might also want to look at

* an Ansible role for [nginx](https://github.com/dhruvbansal/nginx-ansible-role)
* an Ansible role for [ElasticSearch](https://github.com/dhruvbansal/elasticsearch-ansible-role)
* an Ansible role for [Kibana](https://github.com/dhruvbansal/kibana-ansible-role)

# What it Does

Creats a virtual host for nginx that forwards requests back to Marvel
AND ElasticSearch, making it so that you don't need to give full
access to port 9200 on your ElasticSearch cluster to the same browser
that is using Marvel.  Authentication is handled via an nginx
firewall.

Also creates a logstash input file for Marvel's logs.  Disable this by
setting `marvel_use_logstash` to `false`.

## Assumptions

Assumes you have already installed nginx, ElasticSearch and that you
have a backend in your nginx configuration like this

```
upstream elasticsearch {
  server localhost:9200;
  ...
}
```

and a firewall and proxy rules defined already for nginx
(`marvel_firewall_rules` and `marvel_proxy_params`).

## Configuration & Logging

Creates the files:

* `/etc/nginx/sites-available/marvel.conf` -- nginx virtual host for marvel
* `/var/log/marvel` -- log directory
* `/etc/logstash/conf.d/marvel.conf` -- inputs for logstash

## Services

Creates an `nginx` virtual host.

# Usage

Use the role in a playbook like this:

```yaml
- hosts: all
  roles:
    - marvel
```

## Variables

The following variables are exposed for configuration:

* `marvel_user` -- Marvel user (default: www-data)
* `marvel_group` -- Marvel group (default: www-data)
* `marvel_fqdn` -- Host nginx virtual server will listen for (e.g. - marvel.example.com)
* `marvel_elasticsearch_url` -- full URL for nginx virtual server (e.g. - https://marvel.example.com)
* `marvel_firewall_rules` -- path to nginx firewall configuration
* `marvel_proxy_params` -- path to nginx proxy params
* `marvel_kibana_index` -- name of ElasticSearch index used by Marvel for Kibana (default: .marvel-int)
* `marvel_use_logstash` -- set to `false` to skip logstash configuration

Ensure that `marvel_elasticsearch_url` and `marvel_fqdn` match.  It is
**not** necessary to directly connect the end-user's browser to port
9200 on the ElasticSearch cluster.  The point of the nginx virtual
server created by this role is to obviate that.  The nginx virtual
server will proxy traffic for **both** Marvel and ElasticSearch.
