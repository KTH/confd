# confd

[![Build Status](https://travis-ci.org/kelseyhightower/confd.svg?branch=master)](https://travis-ci.org/kelseyhightower/confd)

## KTH proprietary stuff

To develop locally, do the following:

* Create /tmp in project folder with subdirs /conf.d and /templates
* Add confd.conf to /tmp, haproxy.toml to /tmp/conf.d and haproxy.conf.tmpl to /templates
* Contents of confd.conf:
```backend = "etcd"```

* Contents of haproxy.toml:
```
[template]
src = "haproxy.conf.tmpl"
dest = "haproxy.new"
keys = [
    "/services"
]
```

* Content of the haproxy.conf.tmpl should be the template you want to test
* Run a local etcd instanc:

```docker run -d -p 4001:4001 -p 7001:7001 -v /tmp/etcd/:/data microbox/etcd:latest -name etcd```

* Insert a default service key:

```etcdctl set /services/kth-azure-lb-hadefault-80/kth-azure-lb-hadefault '{"port":32768,"ip":"10.28.21.134","tags":["app","kth","web","nginx"],"attrs":{"default":"true"}}'```

* Insert a couple of published services:

```etcdctl set /services/kth-azure-app/everest-blue-worker1:kthazureapp_web_1:3000 '{"port":32769,"ip":"10.28.21.132","tags":["app","kth","web","nodejs"],"attrs":{"publish":"/_azure/_monitor"}}'```

```etcdctl set /services/kth-azure-app/everest-blue-worker2:kthazureapp_web_1:3000 '{"port":32770,"ip":"10.28.21.133","tags":["app","kth","web","nodejs"],"attrs":{"publish":"/_azure/_monitor"}}'```

* Build confd: 

```./build```

* Run confd: 

```./bin/confd -onetime -confdir /Users/tinglev/Repos/golang/src/github.com/kelseyhightower/confd/tmp -log-level debug```

* haproxy.new should now be available at the root of the project

`confd` is a lightweight configuration management tool focused on:

* keeping local configuration files up-to-date using data stored in [etcd](https://github.com/coreos/etcd),
  [consul](http://consul.io), [dynamodb](http://aws.amazon.com/dynamodb/), [redis](http://redis.io),
  [vault](https://vaultproject.io), [zookeeper](https://zookeeper.apache.org) or env vars and processing [template resources](docs/template-resources.md).
* reloading applications to pick up new config file changes

## Community

* IRC: `#confd` on Freenode
* Mailing list: [Google Groups](https://groups.google.com/forum/#!forum/confd-users)
* Website: [www.confd.io](http://www.confd.io)

## Building

Go 1.6 is required to build confd, which uses the new vendor directory.

```
$ mkdir -p $GOPATH/src/github.com/kelseyhightower
$ git clone https://github.com/kelseyhightower/confd.git $GOPATH/src/github.com/kelseyhightower/confd
$ cd $GOPATH/src/github.com/kelseyhightower/confd
$ ./build
```

You should now have confd in your `bin/` directory:

```
$ ls bin/
confd
```

## Getting Started

Before we begin be sure to [download and install confd](docs/installation.md).

* [quick start guide](docs/quick-start-guide.md)

## Next steps

Check out the [docs directory](docs) for more docs.
