---
layout: post
title: "ManageIQ 설치"
date: 2021-02-06 12:57:00 +0900
categories: [Blogging]
tags: [manageiq]
---

## 설치 에러

```bash
ERROR: pkg-config is required to build Rugged.
sudo apt-get install pkg-config

bundle exec rake
rake aborted!
Errno::ENOENT: No such file or directory @ rb_sysopen - /mnt/d/100.Workspace/vc_workspace/manageiq/log/evm.log
```
--> /log 폴더 만들어주니 수행됨

~~~bash
linebird@DESKTOP-KK231TC:/mnt/d/100.Workspace/vc_workspace/manageiq$ bundle exec rake
** Using session_store: ActionDispatch::Session::MemoryStore
** ManageIQ master, codename: Lasker
~~~

- Error1. windows의 wsl에서 memcached 명령 에러
```bash
linebird@DESKTOP-KK231TC:/mnt/d/100.Workspace/vc_workspace/manageiq$ sudo systemctl start memcached
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```
Solve1.
```
sudo /etc/init.d/memcached start
```

- Error2. windows의 wsl에서 postgresql 명령 에러
```bash
linebird@DESKTOP-KK231TC:/mnt/d/100.Workspace/vc_workspace/manageiq$ sudo systemctl restart postgresql
System has not been booted with systemd as init system (PID 1). Can't operate.
Failed to connect to bus: Host is down
```
Solved2.
```bash
sudo /etc/init.d/postgresql restart
sudo su postgres -c "psql -c \"CREATE ROLE root SUPERUSER LOGIN PASSWORD 'smartvm'\""
```

- Error3. 비밀번호가 틀리다고 에러날 때...  
Solved3.
```bash
sudo vi /etc/postgresql/10/main/pg_hba.conf
# PostgreSQL Client Authentication Configuration File
# ===================================================
#
# Refer to the "Client Authentication" section in the PostgreSQL
# documentation for a complete description of this file.  A short
# synopsis follows.
#
# This file controls: which hosts are allowed to connect, how clients
# are authenticated, which PostgreSQL user names they can use, which
# databases they can access.  Records take one of these forms:
#
# local      DATABASE  USER  METHOD  [OPTIONS]
# host       DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostssl    DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
# hostnossl  DATABASE  USER  ADDRESS  METHOD  [OPTIONS]
#
# (The uppercase items must be replaced by actual values.)
#
# The first field is the connection type: "local" is a Unix-domain
# socket, "host" is either a plain or SSL-encrypted TCP/IP socket,
# "hostssl" is an SSL-encrypted TCP/IP socket, and "hostnossl" is a
# plain TCP/IP socket.
#
# DATABASE can be "all", "sameuser", "samerole", "replication", a
# database name, or a comma-separated list thereof. The "all"
# keyword does not match "replication". Access to replication
# must be enabled in a separate record (see example below).
#
# USER can be "all", a user name, a group name prefixed with "+", or a
# comma-separated list thereof.  In both the DATABASE and USER fields
# you can also write a file name prefixed with "@" to include names
# from a separate file.
#
# ADDRESS specifies the set of hosts the record matches.  It can be a
# host name, or it is made up of an IP address and a CIDR mask that is
# an integer (between 0 and 32 (IPv4) or 128 (IPv6) inclusive) that
# specifies the number of significant bits in the mask.  A host name
# that starts with a dot (.) matches a suffix of the actual host name.
# Alternatively, you can write an IP address and netmask in separate
# columns to specify the set of hosts.  Instead of a CIDR-address, you
# can write "samehost" to match any of the server's own IP addresses,
# or "samenet" to match any address in any subnet that the server is
# directly connected to.
#
# METHOD can be "trust", "reject", "md5", "password", "scram-sha-256",
# "gss", "sspi", "ident", "peer", "pam", "ldap", "radius" or "cert".
# Note that "password" sends passwords in clear text; "md5" or
# "scram-sha-256" are preferred since they send encrypted passwords.
#
# OPTIONS are a set of options for the authentication in the format
# NAME=VALUE.  The available options depend on the different
# authentication methods -- refer to the "Client Authentication"
# section in the documentation for a list of which options are
# available for which authentication methods.
#
# Database and user names containing spaces, commas, quotes and other
# special characters must be quoted.  Quoting one of the keywords
# "all", "sameuser", "samerole" or "replication" makes the name lose
# its special character, and just match a database or username with
# that name.
#
# This file is read on server startup and when the server receives a
# SIGHUP signal.  If you edit the file on a running system, you have to
# SIGHUP the server for the changes to take effect, run "pg_ctl reload",
# or execute "SELECT pg_reload_conf()".
#
# Put your actual configuration here
# ----------------------------------
#
# If you want to allow non-local connections, you need to add more
# "host" records.  In that case you will also need to make PostgreSQL
# listen on a non-local interface via the listen_addresses
# configuration parameter, or via the -i or -h command line switches.




# DO NOT DISABLE!
# If you change this first entry you will need to make sure that the
# database superuser can access the database using some other method.
# Noninteractive access to all databases is required during automatic
# maintenance (custom daily cronjobs, replication, and similar tasks).
#
# Database administrative login by Unix domain socket
local   all             postgres                                md5

# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     md5  <-- peer 수정 
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5  <-- peer 수정
# IPv6 local connections:
host    all             all             ::1/128                 md5  
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     md5  <-- peer 수정
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
```

## manageiq 구동

1. manageiq 구동
```bash
sudo /etc/init.d/memcached start
sudo /etc/init.d/postgresql start
bundle exec rails server
```

2. postgresql 접속
```bash
psql -h localhost -p 5432 -U jini -d vmdb_development
```

## PostgreSQL과 관련 패키지 전체 삭제하기

```bash
sudo apt-get --purge remove postgresql\*
dpkg -l | grep postgres
```

### ERROR1. postgresql_adapter.rb:696:in `rescue in connect': FATAL:  database "vmdb_development" does not exist (ActiveRecord::NoDatabaseError)

```bash
bundle exec rake db:create db:migrate db:seed
```


## manageiq 와 manageiq-ui-classic 소스 연결 방법

1. symbolic link 생성

```bash
ln -s /home/u/miq/manageiq /home/u/miq/manageiq-ui-classic/spec/manageiq
ln -s /mnt/d/100.Workspace/vc_workspace/miq/manageiq /mnt/d/100.Workspace/vc_workspace/miq/manageiq-ui-classic/spec/manageiq
```
최초 manageiq-ui-classic 디렉토리에는 spec/manageiq가 존재하지 않는다.  
manageiq-ui-classic 디렉토리에서 bin/setup을 실행하면, spec/manageiq 디렉토리가 생긴다.

2. manageiq의 다음 디렉토리에 아래 파일을 생성한다. "/home/u/miq/manageiq/bundler.d/overrides.rb"

```bash
override_gem "manageiq-ui-classic", path: "/home/u/miq/manageiq-ui-classic"
override_gem "manageiq-ui-classic", path: "/mnt/d/100.Workspace/vc_workspace/miq/manageiq-ui-classic"
```

3. 이제 /home/u/miq/manageiq 폴더에서 bin/update를 실행 한 다음 /home/u/miq/manageiq-ui-classic 폴더에서 bin/update를 실행. /home/u/miq/manageiq 폴더에서 아래 실행.

```bash
bundle install
bundle exec rake
```

## SSO 구현 방법(csrf 우회하도록 소스 수정)

manageiq-ui-classic/app/controllers/dashboard_controller.rb 수정
```ruby
class DashboardController < ApplicationController
  include Mixins::BreadcrumbsMixin
  include DashboardHelper
  include StartUrl


  menu_section :vi

  @@items_per_page = 8

  before_action :check_privileges, :except => %i[csp_report authenticate
                                                 external_authenticate kerberos_authenticate
                                                 logout login login_retry wait_for_task
                                                 saml_login initiate_saml_login
                                                 oidc_login initiate_oidc_login
                                                 sso_login]
  before_action :get_session_data, :except => %i[csp_report authenticate
                                                 external_authenticate kerberos_authenticate saml_login oidc_login]
  after_action :cleanup_action,    :except => %i[csp_report]

  # 유진수 추가 csrf 우회
  skip_before_action :verify_authenticity_token
  ...

  def AESCrypt.encrypt(password, iv, cleardata)
    cipher = OpenSSL::Cipher.new('AES-256-CBC')
    cipher.encrypt  # set cipher to be encryption mode
    cipher.key = password
    cipher.iv  = iv

    encrypted = ''
    encrypted << cipher.update(cleardata)
    encrypted << cipher.final
    AESCrypt.b64enc(encrypted)
  end

  def AESCrypt.decrypt(password, iv, secretdata)
    secretdata = Base64::decode64(secretdata)
    decipher = OpenSSL::Cipher::Cipher.new('aes-256-cbc')
    decipher.decrypt
    decipher.key = password
    decipher.iv = iv if iv != nil
    decipher.update(secretdata) + decipher.final
  end

  def AESCrypt.b64enc(data)
    Base64.encode64(data).gsub(/\n/, '')
  end

  # 유진수 추가 sso_login
  def sso_login
    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    puts  params[:data]
    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"

    crypto_key = 'Arom12#$123456781234567812345678'
    ssodata = AESCrypt.decrypt('Arom12#$123456781234567812345678', crypto_key[0..15], params[:data].gsub(/[ ]/, '+'))
    user_name, user_password = ssodata.split(/:/)

    # user = {
    #   :name            => params[:aaa],
    #   :password        => params[:bbb]
    # }
    user = {
      :name            => user_name,
      :password        => user_password
    }

    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    puts user
    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    
    validation = validate_user(user, params[:task_id], request)
    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    puts validation
    puts ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    case validation.result
    when :wait_for_task
      # noop, page content already set by initiate_wait_for_task
    when :pass
      render :update do |page|
        page << javascript_prologue
        # page.redirect_to(validation.url)

        # page.redirect_to가 정상 동작하지 않아, controller.redirect_to 사용
        controller.redirect_to(validation.url)
      end
    when :fail
      clear_current_user
      add_flash(validation.flash_msg || _("Error: Authentication failed"), :error)
      render :update do |page|
        page << javascript_prologue
        page.replace("flash_msg_div", :partial => "layouts/flash_msg")
        page << javascript_show("flash_div")
        page << "miqAjaxAuthFail();"
        page << "miqSparkle(false);"
      end
    end
  end  
  ...
```

## Cloud 제공자 삭제 방법
> manageiq-ui-classic/app/controllers/mixin/ems_common.rb 수정. form_instance_vars 메소드 수정
```ruby

    def form_instance_vars
      @server_zones = []
      zones = Zone.visible.order('lower(description)')
      zones.each do |zone|
        @server_zones.push([zone.description, zone.name])
      end
      @ems_types = Array(model.supported_types_and_descriptions_hash.invert).sort_by(&:first)

      # 사용하지 않는 cloud provider 제거(수정 부분) 
      @ems_types.delete_at(2)
      @ems_types.delete_at(3)
      @ems_types.delete_at(3)

      @provider_regions = retrieve_provider_regions
      @openstack_infra_providers = retrieve_openstack_infra_providers
      @openstack_security_protocols = retrieve_openstack_security_protocols
      @amqp_security_protocols = retrieve_amqp_security_protocols
      @nuage_security_protocols = retrieve_nuage_security_protocols
      @container_security_protocols = retrieve_container_security_protocols
      @scvmm_security_protocols = [[_('Basic (SSL)'), 'ssl'], ['Kerberos', 'kerberos']]
      @openstack_api_versions = retrieve_openstack_api_versions
      @vmware_cloud_api_versions = retrieve_vmware_cloud_api_versions
      @azure_stack_api_versions = retrieve_azure_stack_api_versions
      @emstype_display = model.supported_types_and_descriptions_hash[@ems.emstype]
      if @ems.respond_to?(:description)
        @ems_region_display = @ems.description
      end
      @nuage_api_versions = retrieve_nuage_api_versions
      @hawkular_security_protocols = retrieve_hawkular_security_protocols
      @redfish_security_protocols = retrieve_security_protocols
    end

```


## ManageIQ 브라우저 탭 타이틀 수정

> "ManageIQ" --> "Hybrid Cloud 관리 플랫폼" 으로 수정 요청(KT)

manageiq source에서 manageiq/locale/ko.yml 수정
```yaml
ko:
  product:
    # name: ManageIQ
    # name_full: ManageIQ
    name: Hybrid Cloud 관리 플랫폼
    name_full: Hybrid Cloud 관리 플랫폼
    copyright: "Copyright (c) 2020 ManageIQ. Sponsored by Red Hat Inc."

  # Used in NumberHelper.number_to_human_size() and NumberHelper.number_to_human()
  number:
    human:
      storage_units:
        units:
          pb: "PB"
          eb: "EB"
```


