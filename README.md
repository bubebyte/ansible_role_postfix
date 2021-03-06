# postfix

## Description
Role to install and configure postfix.

## Example Playbook
```YAML
---
- name: Postfix
  hosts: all
  become: true
  gather_facts: true

  roles:
    - role: bubebyte.postfix
```

## Role Variables
These are the role default which are set in default/main.yml:
```YAML
---
postfix_extra_packages: []
postfix_mysql_map_support: false
postfix_cdb_map_support: false
postfix_ldap_map_support: false
postfix_pcre_map_support: false
postfix_pgsql_map_support: false
postfix_sqlite_map_support: false

postfix_sql_backend_hostname: localhost
postfix_sql_backend_databasename: maildb
postfix_sql_backend_user: maildbusr
postfix_sql_backend_password: "ChangeMePLZ!"
postfix_virtual_domains_query: "SELECT DISTINCT domain FROM users WHERE active = 'Y' AND domain = '%s';"
postfix_virtual_mailbox_aliases_query: "SELECT target FROM aliases WHERE alias = '%s';"
postfix_virtual_mailbox_recipients_query: "SELECT home FROM users WHERE active = 'Y' AND concat(username,'@', domain) = '%s';"

postfix_configuration:
  default:
    compatibility_level: 2
    queue_directory: /var/spool/postfix
    command_directory: /usr/sbin
    daemon_directory: /usr/libexec/postfix
    data_directory: /var/lib/postfix
    mail_owner: postfix
    inet_interfaces: localhost
    inet_protocols: all
    mydestination: $myhostname, localhost.$mydomain, localhost
    unknown_local_recipient_reject_code: 550
    alias_maps: "hash:/etc/aliases"
    alias_database: "hash:/etc/aliases"
    debug_peer_level: 2
    debugger_command: "PATH=/bin:/usr/bin:/usr/local/bin:/usr/X11R6/bin ; ddd $daemon_directory/$process_name $process_id & sleep 5"
    sendmail_path: /usr/sbin/sendmail.postfix
    newaliases_path: /usr/bin/newaliases.postfix
    mailq_path: /usr/bin/mailq.postfix
    setgid_group: postdrop
    html_directory: "no"
    manpage_directory: /usr/share/man
    sample_directory: /usr/share/doc/postfix/samples
    readme_directory: /usr/share/doc/postfix/README_FILES
    smtpd_tls_cert_file: /etc/pki/tls/certs/postfix.pem
    smtpd_tls_key_file: /etc/pki/tls/private/postfix.key
    smtpd_tls_security_level: may
    smtp_tls_CApath: /etc/pki/tls/certs
    smtp_tls_CAfile: /etc/pki/tls/certs/ca-bundle.crt
    smtp_tls_security_level: may
    meta_directory: /etc/postfix
    shlib_directory: /usr/lib64/postfix

postfix_master_configuration:
  - {name: smtp, type: inet, private: n, chroot: n, command: smtpd}
  - {name: pickup, type: unix, private: n, chroot: n, wakeup: 60, maxproc: 1, command: pickup}
  - {name: cleanup, type: unix, private: n, chroot: n, maxproc: 0, command: cleanup}
  - {name: qmgr, type: unix, private: n, chroot: n, wakeup: 300, maxproc: 1, command: qmgr}
  - {name: tlsmgr, type: unix, chroot: n, wakeup: '1000?', maxproc: 1, command: tlsmgr}
  - {name: rewrite, type: unix, chroot: n, command: trivial-rewrite}
  - {name: bounce, type: unix, chroot: n, maxproc: 0, command: bounce}
  - {name: defer, type: unix, chroot: n, maxproc: 0, command: bounce}
  - {name: trace, type: unix, chroot: n, maxproc: 0, command: bounce}
  - {name: verify, type: unix, chroot: n, maxproc: 1, command: verify}
  - {name: flush, type: unix, private: n, chroot: n, wakeup: '1000?', maxproc: 0, command: flush}
  - {name: proxymap, type: unix, chroot: n, command: proxymap}
  - {name: proxywrite, type: unix, chroot: n, maxprox: 1, command: proxymap}
  - {name: smtp, type: unix, chroot: n, command: smtp}
  - name: relay
    type: unix
    chroot: n
    command: smtp
    argument_lines:
      - '-o syslog_name=postfix/$service_name'
  - {name: showq, type: unix, private: n, chroot: n, command: showq}
  - {name: error, type: unix, chroot: n, command: error}
  - {name: retry, type: unix, chroot: n, command: error}
  - {name: discard, type: unix, chroot: n, command: discard}
  - {name: local, type: unix, unpriv: n, chroot: n, command: local}
  - {name: virtual, type: unix, unpriv: n, chroot: n, command: virtual}
  - {name: lmtp, type: unix, chroot: n, command: lmtp}
  - {name: anvil, type: unix, chroot: n, maxprox: 1, command: anvil}
  - {name: scache, type: unix, chroot: n, maxprox: 1, command: scache}

postfix_mysql_map_configuration:
  mysql_configuration:
    virtual_mailbox_domains: "mysql:/etc/postfix/mysql_map/virtual_domains.cf"
    virtual_mailbox_maps: "mysql:/etc/postfix/mysql_map/virtual_mailbox_recipients.cf"
    virtual_alias_maps: "mysql:/etc/postfix/mysql_map/virtual_mailbox_aliases.cf"

postfix_smtpd_restrictions:
  helo: []
  recipient: []
  sender: []
  client: []
  data: []
```

## Requirements

### Roles
none

### Collections
none

## License
MIT License

## Author Information
[Armin Bube](https://bubebyte.de)
