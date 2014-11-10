This is an [Ansible](http://www.ansible.com/home) role for a
reasonably secure installation of
[Marvel](http://www.elasticsearch.org/overview/marvel).  Marvel sits
on top of [ElasticSearch](http://www.elasticsearch.org/) and
[nginx](http://nginx.org) is used to provide a virtual server.

It assumes an
[nginx upstream](http://nginx.org/en/docs/http/ngx_http_upstream_module.html) backend named
`elasticsearch` exists.  Define it like this somewhere

```
upstream elasticsearch {
  server localhost:9200;
  ...
}
```

The server defined for Marvel (`marvel_fqdn`) should match the
ElasticSearch URL used by Marvel (`marvel_elasticsearch_url`) as nginx
will proxy traffic for both Marvel and ElasticSearch via the same
virtual host.

Firewall rules are proxy handling parameters are assumed to be defined
in existing nginx configuration files (`marvel_firewall_rules` and
`marvel_proxy_params`).

A logstash input file is also defined for the nginx logs created by
the virtual server.
