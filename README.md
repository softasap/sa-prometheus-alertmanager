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
