awx-mgmt
=========

    Manage AWX content

Role Variables
--------------

    awx_mgmt_tower_host:     awx.domain.pro
    awx_mgmt_tower_username: ''
    awx_mgmt_tower_password: ''
    
    awx_mgmt_jobs:      []
    awx_mgmt_workflows: []
    
    awx_mgmt_template_inventory:                      ''
    awx_mgmt_template_organiztion:                    ''
    awx_mgmt_template_project:                        ''
    awx_mgmt_template_credentials:                    []
    awx_mgmt_template_extra_vars:                     {}
    awx_mgmt_template_job_tags:                       []
    awx_mgmt_template_limit:                          ''
    awx_mgmt_template_notification_templates_error:   []
    awx_mgmt_template_notification_templates_started: []
    awx_mgmt_template_notification_templates_success: []
    awx_mgmt_template_skip_tags:                      ''
    awx_mgmt_template_ask_credential_on_launch:       no
    awx_mgmt_template_ask_variables_on_launch:        on
    awx_mgmt_template_ask_diff_mode_on_launch:        on
    awx_mgmt_template_ask_extra_vars:                 no
    awx_mgmt_template_ask_inventory:                  no

Dependencies
------------

    ansible-galaxy collection install awx.awx

Example Playbook
----------------

    - hosts: localhost
      vars:
        awx_mgmt_tower_host: awx.domain.pro
        awx_mgmt_tower_username: ''
        awx_mgmt_tower_password: ''
        awx_mgmt_template_organiztion: Default
        awx_mgmt_template_project: infrastructure
        awx_mgmt_template_inventory: '{{ env }}'
        awx_mgmt_template_credentials:
          - ecosystem
          - main
          - sec
        awx_vars_deploy_telegram_username: '{% raw %}{{ tower_user_name }}{% endraw %}'
        awx_mgmt_jobs:
          - { state: absent,  name: prod-backend-a, playbook: backend-ecosystem-k8s.yml, extra_vars: { play_group: k8s_masters_a, deploy_telegram_notify: true, deploy_telegram_username: '{% raw %}{{ awx_vars_deploy_telegram_username }}{% endraw %}', build_branch: master, build_unit: true, build_versions: [] } }
          - { state: present, name: prod-backend-b, playbook: backend-ecosystem-k8s.yml, extra_vars: { play_group: k8s_masters_b, deploy_telegram_notify: true, deploy_telegram_username: '{% raw %}{{ awx_vars_deploy_telegram_username }}{% endraw %}', build_branch: master, build_unit: true, build_versions: [] } }
      pre_tasks:
        - block:
          - pause:
            prompt: awx_mgmt_tower_username
            echo: yes
            register: result
          - name: set awx_mgmt_tower_username from user input
            set_fact:
              awx_mgmt_tower_username: '{{ result.user_input }}'
          when: awx_mgmt_tower_username is not defined or awx_mgmt_tower_username == ''
        - block:
          - pause:
            prompt: awx_mgmt_tower_password
            echo: no
            register: result
          - name: set awx_mgmt_tower_password from user input
            set_fact:
              awx_mgmt_tower_password: '{{ result.user_input }}'
          when: awx_mgmt_tower_password is not defined or awx_mgmt_tower_password == ''
      roles:
        - name: awx-mgmt

License
-------

    MIT

Author Information
------------------

    Dmitrij Petrov
