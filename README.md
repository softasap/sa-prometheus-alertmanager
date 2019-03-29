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

Reasonable initial config
-------------------------

Take a look on suggested structure for multicomponent product with notification via slack and email

```

global:
  # The smarthost and SMTP sender used for mail notifications.
  smtp_smarthost: 'smtp.gmail.com:587'
  smtp_from: 'email@google.apps'
  smtp_auth_username: 'email@google.apps'
  smtp_auth_password: 'some pass'

templates:
- '/etc/prometheus_alertmanager/template/*.tmpl'

# The root route on which each incoming alert enters.
route:
  # The labels by which incoming alerts are grouped together. For example,
  # multiple alerts coming in for cluster=A and alertname=LatencyHigh would
  # be batched into a single group.
  group_by: ['alertname', 'cluster', 'service']

  # When a new group of alerts is created by an incoming alert, wait at
  # least 'group_wait' to send the initial notification.
  # This way ensures that you get multiple alerts for the same group that start
  # firing shortly after another are batched together on the first
  # notification.
  group_wait: 30s

  # When the first notification was sent, wait 'group_interval' to send a batch
  # of new alerts that started firing for that group.
  group_interval: 5m

  # If an alert has successfully been sent, wait 'repeat_interval' to
  # resend them.
  repeat_interval: 3h

  # A default receiver
  receiver: slack-channel

  # All the above attributes are inherited by all child routes and can
  # overwritten on each.

  # The child route trees.
  routes:
  # This routes performs a regular expression match on alert labels to
  # catch alerts that are related to a list of services.
  - match_re:
      service: ^(foo1|foo2|baz)$
    receiver: slack-channel
    # The service has a sub-route for critical alerts, any alerts
    # that do not match, i.e. severity != critical, fall-back to the
    # parent node and are sent to 'team-X-mails'
    routes:
    - match:
        severity: critical
      receiver: slack-channel
  - match:
      service: files
    receiver: slack-channel

    routes:
    - match:
        severity: critical
      receiver: slack-channel

  # This route handles all alerts coming from a database service. If there's
  # no team to handle it, it defaults to the DB team.
  - match:
      service: database
    receiver: slack-channel
    # Also group alerts by affected database.
    group_by: [alertname, cluster, database]
    routes:
    - match:
        owner: team-X
      receiver: slack-channel
    - match:
        owner: team-Y
      receiver: slack-channel


# Inhibition rules allow to mute a set of alerts given that another alert is
# firing.
# We use this to mute any warning-level notifications if the same alert is
# already critical.
inhibit_rules:
- source_match:
    severity: 'critical'
  target_match:
    severity: 'warning'
  # Apply inhibition if the alertname is the same.
  equal: ['alertname', 'cluster', 'service']


receivers:
- name: slack-channel
  email_configs:
  - to: customer@domain.com
  - to: team@domain.com
  slack_configs:
# customer slack
  - channel: #notifications
    send_resolved: true
    api_url: https://hooks.slack.com/services/....
    icon_url: https://avatars3.githubusercontent.com/u/4029521
    title: '{{ template "custom_title" . }}'
    text: '{{ template "custom_slack_message" . }}'
# your company ops slack    
  - channel: "system_alerts"
    send_resolved: true
    api_url: https://hooks.slack.com/services/.....
    icon_url: https://avatars3.githubusercontent.com/u/4029521
    title: '{{ template "custom_title" . }}'
    text: '{{ template "custom_slack_message" . }}'


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
