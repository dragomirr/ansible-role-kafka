# Ansible kafka role

Ansible role for install kafka with KRaft mode on Debian and RedHat distributions. KRaft is marked production ready in version 3.3.1 so this role should be used to deploy kafka version equal or greater than 3.3.1.

Tested with ubuntu Debian13 but should work with most distros, especially when not using this role to install dependencies. See `kafka_install_dependencies` variable.

For more info check [changelog](https://github.com/dragomirr/ansible-role-kafka/blob/main/CHANGELOG.md).

## Requirements

* x86_64 arch on servers that will run kafka
* ansible 2.21 -- did not test with other versions, but probably would work with anything equal or greater than version 2.10
* Java installed -- version 8(deprecated), 11, 17 or 21. -- `apt install openjdk-21-jre-headless` or `dnf install java-21-openjdk-headless`
* For ubuntu setfacl from `acl` package -- `apt install acl`

## Role Variables

Required:

  * `kafka_node_id` -- kafka node id. Must be integer or zero and unique per host.

Optional:

  * `kafka_topics` -- list of kafka topics with topic settings.
  * `kafka_install_dependencies` -- should java be installed and also `acl` for Debian based distributions. For now it can be installed using role setting this to `true`.
  * `kafka_config_path` -- if unset config will be deployed to kafka home. Should not be set to default config location as subsequent role runs will not be idempotent and will cause kafka restart.
  * `kafka_additional_config` -- specify map of config parameters that are not defined by role.
  * `kafka_opts` -- specify list or string of kafka options to run at startup. From this variable `KAFKA_OPTS` environment variable will be created.

## Dependencies

None with `kafka_install_dependencies=true`. For other cases look at the requirements section.

## Example Playbook

Install role using ansible-galaxy `ansible-galaxy install dragomirr.kafka`


    - hosts: servers
      roles:
         - role: dragomirr.kafka
           # setting kafka_node_id in play is only valid if you have 1 kafka node
           # if you have multiple kafka nodes you need to set unique kafka_node_id for each node
           kafka_node_id: 0
           kafka_heap_size: 2G
           kafka_install_dependencies: true
           kafka_topics:
             - name: topic1
             - name: topic2
               replication_factor: 1
               partitions: 10
          # adding config that is not defined by role
          kafka_additional_config:
            message.max.bytes: 10000
          # adding kafka startup options
          kafka_opts:
            - -XX:NewSize=256m

## License

GPL3

## Testing

Testing is done using [molecule](https://molecule.readthedocs.io/) with [virtualbox](https://www.virtualbox.org/) and [vagrant](https://www.vagrantup.com/)

There are 3 scenarios:

  * Default scenario using 1 instance.
  * Cluster scenario using 3 instances with both controller and broker role for all instances.
  * Cluster combined scenario using 6 instances where 3 are in broker and controller role and 3 in only broker role.

Run tests:

```bash
# install molecule
pip install molecule

# install vagrant plugin
pip install molecule-plugins[ec2]

# run test
molecule test
```