sa-prometheus-alertmanager
==========================

[![Build Status](https://travis-ci.org/softasap/sa-prometheus-alertmanager.svg?branch=master)](https://travis-ci.org/softasap/sa-prometheus-alertmanager)

Compatible Web-UI:  https://github.com/cloudflare/unsee

Graphing your alerting tree:  https://prometheus.io/webtools/alerting/routing-tree-editor/

Ideas for alerts

Gitlab project:
	https://gitlab.com/gitlab-com/runbooks/tree/master/alerts

rubustperception wiki:
	http://tips.robustperception.io/

Example of usage:

Simple

```YAML

     - {
         role: "sa-prometheus-alertmanager"
       }
```

Advanced

```YAML

roles:

     - {
         role: "sa-prometheus-alertmanager",
         prometheus_alertmanager_user:   prometheus,
         prometheus_alertmanager_group:  prometheus,
         prometheus_alertmanager_base_dir: /opt/prometheus_alertmanager,
         prometheus_alertmanager_version: "0.14.0"
       }
```


Notes on installed state
------------------------

Systemd startup file will look like this:

```
# CI/CD managed
# Do NOT modify this file by hand!

[Unit]
Description=Prometheus Alertmanager
After=network.target
Requires=network.target

[Service]
EnvironmentFile=/etc/prometheus_alertmanager/prometheus_alertmanager_configuration

ExecStart=/opt/prometheus_alertmanager/alertmanager/alertmanager --config.file=/etc/prometheus_alertmanager/config.yml --storage.path=/opt/prometheus_alertmanager/data $OPTIONS
User=prometheus
Group=prometheus


Restart=always
RestartSec=5
TimeoutSec=5

[Install]
WantedBy=multi-user.target
```

```
ls -la /opt/prometheus_alertmanager/data
total 8
drwxr-x--- 2 prometheus prometheus 4096 Jul 18 13:35 .
drwxr-x--- 4 prometheus prometheus 4096 Apr  7 22:03 ..
-rw-r----- 1 prometheus prometheus    0 Jul 18 13:35 nflog
-rw-r----- 1 prometheus prometheus    0 Jul 18 13:35 silences
```


Usage with ansible galaxy workflow
----------------------------------

If you installed the `sa-prometheus-alertmanager` role using the command


`
   ansible-galaxy install softasap.sa-prometheus-akertmanager
`

the role will be available in the folder `library/softasap.sa-prometheus-alertmanager`
Please adjust the path accordingly.

```YAML

     - {
         role: "softasap.sa-prometheus-alertmanager"
       }

```




Copyright and license
---------------------

Code is dual licensed under the [BSD 3 clause] (https://opensource.org/licenses/BSD-3-Clause) and the [MIT License] (http://opensource.org/licenses/MIT). Choose the one that suits you best.

Reach us:

Subscribe for roles updates at [FB] (https://www.facebook.com/SoftAsap/)

Join gitter discussion channel at [Gitter](https://gitter.im/softasap)

Discover other roles at  http://www.softasap.com/roles/registry_generated.html

visit our blog at http://www.softasap.com/blog/archive.html
