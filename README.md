uWSGI Consul integration
========================

This draft aims at explaining how uWSGI could support service discovery and other tools provided by Consul (http://consul.io). The main idea is that the dynamic application loading feature provided by the uWSGI Emperor mode can be extended by the service discovery features of Consul to build a fully distributed, self aware and fault tolerant architecture.

## Consul agent definition

The first step would be to implement some new options to uWSGI which would allow to declare one (or multiple for HA ?) consul agents and the overall query consistency mode.

* consul-agent = localhost:XXXX
* consul-consistency-mode = default/consistent/stale

## Service definition

Using emperor mode, uWSGI should be able to register/deregister a given service as it is loaded/unloaded by the emperor. Obviously this should happen *after* the app is successfully loaded and *before* it is unloaded.

Ref : http://www.consul.io/docs/agent/services.html

New options needed :

* consul-service-id = my_id (optional)
* consul-service-name = my_name
* consul-service-tags = tag1,tag2 (optional)
* consul-service-port = 8000 

## Check definition

When a service is registered/deregistered, uWSGI should trigger a service check creation/deletion and then periodically update the service check result based on the app liveness.

Ref : http://www.consul.io/docs/agent/checks.html
Ref : http://www.consul.io/docs/agent/http.html

## Service discovery

The uwsgi python module should provide helpers implementing service discovery.

Ref : http://www.consul.io/docs/agent/http.html

* uwsgi.consul_services(dc)
* uwsgi.consul_service(tag, name, dc)

## K/V store

The uwsgi python module should provide helpers to manipulate data in the Consul K/V store.

Ref : http://www.consul.io/docs/agent/http.html

* uwsgi.consul_set(name, value, dc, cas, acquire, release)
* uwsgi.consul_get(name, dc)
* uwsgi.consul_del(name, recurse, dc)

## Sessions

The uwsgi module should provide helpers to make use of Consul sessions.

Ref : http://www.consul.io/docs/internals/sessions.html

* uwsgi.consul_session_create(lock_delay, name, node, checks)
* uwsgi.consul_session_destroy(id)
* uwsgi.consul_session_info(id, dc)
* uwsgi.consul_session_node(node, dc)
* uwsgi.consul_session_list(dc)
