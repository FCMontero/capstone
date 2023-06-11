---
- name: Install Docker Engine, Docker Compose, Configure Git
  hosts: 10.0.0.75
  become: true

  tasks:
    - name: Install yum-utils
      yum:
        name: yum-utils
        state: latest

    - name: Install dependencies
      yum:
        name:
          - ca-certificates
          - curl
          - gnupg
          - python3-pip
          - epel-release
        state: present

    - name: Enable Epel extra repo
      yum:
        name: epel-release
        enablerepo: extras
        state: latest

    - name: Install device-mapper-persistent-data
      yum:
        name: device-mapper-persistent-data
        state: latest

    - name: Install lvm2
      yum:
        name: lvm2
        state: latest

    - name: Install Git
      yum:
        name: git
        state: present

    - name: Add and configure docker repo
      get_url:
        url: https://download.docker.com/linux/centos/docker-ce.repo
        dest: /etc/yum.repos.d/docker-ce.repo
      become: true

    - name: Enable Docker edge repo
      ini_file:
        dest: /etc/yum.repos.d/docker-ce.repo
        section: 'docker-ce-edge'
        option: enabled
        value: '0'
      become: true

    - name: Enable Docker Test repo
      ini_file:
        dest: /etc/yum.repos.d/docker-ce.repo
        section: 'docker-ce-test'
        option: enabled
        value: '0'
      become: true

    - name: Install docker-ce
      yum:
        name: docker-ce
        state: present

    - name: Enable and Start Docker service
      systemd:
        name: docker.service
        enabled: yes
        state: started
