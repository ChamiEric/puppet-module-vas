# puppet-module-vas

Puppet module to manage DELL Authentication Services previously known as VAS or Quest Authentication Services

[![Build Status](https://api.travis-ci.org/Ericsson/puppet-module-vas.png)](https://travis-ci.org/Ericsson/puppet-module-vas)

===

# Compatibility

This module has been tested to work on the following systems with Puppet v3
(with and without the future parser) and Puppet v4 with Ruby versions 1.8.7,
1.9.3, 2.0.0 and 2.1.0.

 * RHEL 5
 * RHEL 6
 * RHEL 7
 * Suse 10
 * Suse 11
 * Ubuntu 12.04
 * Ubuntu 14.04
 * Ubuntu 18.04
 * Solaris 9
 * Solaris 10
 * Solaris 11

When using the users.allow functionality in VAS, make sure to set the following option:

<pre>
---
pam::allowed_users:
  - 'ALL'
</pre>
===

Example hiera config:

<pre>
---
vas::username: 'joinuser'
vas::keytab_source: '/net/server/join.keytab'
vas::computers_ou: 'ou=computers,dc=example,dc=com'
vas::users_ou: 'ou=users,dc=example,dc=com'
vas::nismaps_ou: 'ou=nismaps,dc=example,dc=com'
vas::realm: 'realm.example.com'
</pre>


# Facts
The module creates facts as below:
vas_usersallow - A list of entries in /etc/opt/quest/vas/users.allow.
vas_domain - The domain that the host belongs to.
vas_server_type - The server types (GC, DC, PDC).
vas_servers - List of servers that VAS is using for authentication.
vas_site - The AD-site that the host belongs to.
vas_version - The complete version-string for the vas-client.
vasmajversion - The Major version of the vas-client.


# Parameters


package_version
---------------
*Linux only* The VAS package version. Used when upgrading.

- *Default*: 'UNSET'

enable_group_policies
---------------------
Boolean to control if vas should manage group policies. Manages the vasgp package. Version is controlled by package_version.

- *Default*: true

users_allow_entries
-------------------
List of users.allow entries. All users are allowed by default.

- *Default*: ['UNSET']

users_allow_hiera_merge
-----------------------
Boolean to control merges of all found instances of vas::users_allow_entries in Hiera. This is useful for specifying users.allow entries at different levels of the hierarchy and having them all included in the catalog.

This will default to 'true' in future versions.

- *Default*: false

users_deny_entries
------------------
List of users.deny entries. No users are denied by default.

- *Default*: ['UNSET']

users_deny_hiera_merge
----------------------
Boolean to control merges of all found instances of vas::users_deny_entries in Hiera. This is useful for specifying users.deny entries at different levels of the hierarchy and having them all included in the catalog.

This will default to 'true' in future versions.

- *Default*: false

user_override_entries
---------------------
List of user-override entries. Used to override specific user data fields; UID, GID, GECOS, HOME_DIR and SHELL.

- *Default*: ['UNSET']

group_override_entries
---------------------
List of group-override entries. Used to override specific group data fields; GROUP_NAME, GID and GROUP_MEMBERSHIP.

- *Default*: ['UNSET']

username
--------
Name of user account used to join Active Directory.

- *Default*: 'username'

keytab_path
-----------
The path to the keytab file used together with <username> to join Active Directory.

- *Default*: '/etc/vasinst.key'

keytab_source
-------------
File source for the keytab file used to join Active Directory.

- *Default*: undef

keytab_owner
------------
keytab file's owner.

- *Default*: 'root'

keytab_group
------------
keytab file's group.

- *Default*: 'root'

keytab_mode
-----------
keytab file's mode.
- *Default*: '0400'

vas_fqdn
--------
FQDN to join to VAS as.

- *Default*: $::fqdn

computers_ou
------------
Path to OU where to store computer object.

- *Default*: 'UNSET'

users_ou
--------
Deprecated, this parameter is the same as upm_search_path. Path to OU where to load UPM user profiles.

- *Default*: 'UNSET'

nismaps_ou
----------
Path to OU where to load nismaps initially.

- *Default*: 'UNSET'

upm_search_path
---------------
LDAP search path for UPM user profiles. Setting this parameter will cause QAS to run in UPM mode.

- *Default*: 'UNSET'

user_search_path
----------------
LDAP search path for user profiles. This will limit the scope where QAS will search for users when operating in RFC2307 mode.

- *Default*: 'UNSET'

group_search_path
-----------------
LDAP search path for groups. This will limit the scope where QAS will search for groups when operating in RFC2307 mode.

- *Default*: 'UNSET'

realm
-----
Name of the realm.

- *Default*: 'realm.example.com'

domain_realms
-------------
Hash of domains that should be mapped to correct realm.

- *Default*: {}

unjoin_vas
----------
Boolean to trigger an unjoining of the domain. Obviously this will only work if the system is joined to a domain.

- *Default*: false

#### Hiera Example
Assume the hostname of the node is: host2.example.com and realm is REALM2.EXAMPLE.COM.
```
vas::domain_realms:
  fqdn.example.se:
    'example.se'
```

#### Vas.conf Result
```
# This file is being maintained by Puppet.
# DO NOT EDIT
[domain_realm]
 fqdn.example.se = EXAMPLE.SE
 host2.example.com = REALM2.EXAMPLE.COM
```

nisdomainname
-------------
Name of the NIS domain.

- *Default*: undef

sitenameoverride
----------------
Name of AD site to join. The AD site is determined automatically in AD by default.

- *Default*: 'UNSET'

vas_conf_client_addrs
---------------------
client-addrs option in vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_root_update_mode
-------------------------
The value of root-update-mode in the [nss_vas] configuration section. This controls how directory searches will be performed when calling nss functions. See VAS.CONF(5) for more info.

Possible values: force | force-if-missing | none

- *Default*: 'none'

vas_conf_group_update_mode
--------------------------
The value of group-update-mode in the [nss_vas] configuration section. This controls how directory searches will be handeled for group nss functions. See VAS.CONF(5) for more info.

Possible values: force | force-if-missing | none

- *Default*: 'none'

vas_conf_disabled_user_pwhash
-----------------------------
String to be used for disabled-user-pwhash option in vas.conf. If undef, line will be suppressed.

- *Default*: undef

vas_conf_locked_out_pwhash
--------------------------
String to be used for locked-out-pwhash option in vas.conf. If undef, line will be suppressed.

- *Default*: undef

vas_conf_preload_nested_memberships
-----------------------------------
preload-nested-membership option in vas.conf. Set this to 'false' to speed up
flush (and join) operations in VAS version 4.0.3-206 and later.

- *Default*: 'UNSET' (keep default value from VAS)

vas_conf_update_process
-----------------------
update-process option in vas.conf. See VAS.CONF(5) for more info.

- *Default*: '/opt/quest/libexec/vas/mapupdate_2307'

vas_conf_upm_computerou_attr
----------------------------
upm-computerou-attr option in vas.conf. Changed to 'department' to work in a multi-AD-domain setup. See VAS.CONF(5) for more info.

- *Default*: 'department'

vas_conf_full_update_interval
-----------------------------
Integer for number of seconds vasypd will wait until it fully reloads all the NIS maps. See VAS.CONF(5)

- *Default*: 'UNSET' (keep default value from VAS)

vas_conf_vasypd_update_interval
-----------------------------
Integer for number of seconds vasypd will wait between checks for updated NIS Map information in Active Directory. See VAS.CONF(5)

- *Default*: 1800

vas_conf_vasd_update_interval
-----------------------------
Integer for number of seconds to set value of update-interval in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 600

vas_conf_vasd_auto_ticket_renew_interval
----------------------------------------
Integer for number of seconds to set value of auto-ticket-renew-interval in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 32400

vas_conf_vasd_lazy_cache_update_interval
----------------------------------------
Integer for number of minutes for the value of lazy-cache-update-interval in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 10

vas_conf_vasd_timesync_interval
-------------------------------
Integer for number of seconds to set value of timesync-interval in [vasd] section of vas.conf. See VAS.CONF(5) for more info.
If $::virtual is "zone" this value is set to 0

- *Default*: 'UNSET'

vas_conf_vasd_cross_domain_user_groups_member_search
----------------------------------------------------
Boolean to set value of cross-domain-user-groups-member-search in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_password_change_script
------------------------------------
Path for script to set value of password-change-script in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_password_change_script_timelimit
----------------------------------------------
Integer for number of seconds to set value of password-change-script-timelimit in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_workstation_mode
------------------------------
Boolean to control whether or not vasd operates in Workstation mode. See VAS.CONF(5) for more info.

- *Default*: false

vas_conf_vasd_workstation_mode_users_preload
--------------------------------------------
Comma separated list of groups for preloading users in Workstation mode. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_workstation_mode_group_do_member
----------------------------------------------
Boolean to control if vasd should process group memberships in Workstation mode. See VAS.CONF(5) for more info.

- *Default*: false

vas_conf_vasd_workstation_mode_groups_skip_update
-------------------------------------------------
Boolean that can be used to reduce the number of updates by vasd in Workstation mode. See VAS.CONF(5) for more info.

- *Default*: false

vas_conf_vasd_ws_resolve_uid
----------------------------
Boolean to control whether vasd will resolve unknown UIDs when in Workstation mode. See VAS.CONF(5) for more info.

- *Default*: false

vas_conf_vasd_deluser_check_timelimit
-------------------------------------
Integer for number of seconds to set value of deluser-check-timelimit in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_delusercheck_interval
-----------------------------------
Integer for number of minutes to set value of delusercheck-interval in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_delusercheck_script
---------------------------------
Path for script to set value of delusercheck-script in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_netgroup_mode
---------------------------
String to be used to set value of netgroup-mode in the [vasd] section of vas.conf.
Valid values are 'NSS', 'NIS' and 'OFF'. If not specified, the netgroup-mode parameter
will not be set in vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_username_attr_name
--------------------------------
String to be used for username-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'


vas_conf_vasd_groupname_attr_name
---------------------------------
String to be used for groupname-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_uid_number_attr_name
----------------------------------
String to be used for uid-number-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_gid_number_attr_name
----------------------------------
String to be used for gid-number-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_gecos_attr_name
-----------------------------
String to be used for gecos-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_home_dir_attr_name
--------------------------------
String to be used for home-dir-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_login_shell_attr_name
-----------------------------------
String to be used for login-shell-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_group_member_attr_name
------------------------------------
String to be used for group-member-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_memberof_attr_name
--------------------------------
String to be used for memberof-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vasd_unix_password_attr_name
-------------------------------------
String to be used for unix_password-attr-name  in [vasd] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'



vas_conf_prompt_vas_ad_pw
-------------------------
prompt-vas-ad-pw option in vas.conf. Sets the password prompt for logins.

- *Default*: '"Enter Windows password: "'

vas_conf_pam_vas_prompt_ad_lockout_msg
--------------------------------------
prompt-ad-lockout-msg option in vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_libdefaults_default_tgs_enctypes
-----------------------------------------
String to set value of default_tgs_enctypes in [libdefaults] vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'arcfour-hmac-md5'

vas_conf_libdefaults_default_tkt_enctypes
-----------------------------------------
String to set value of default_tkt_enctypes in [libdefaults] vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'arcfour-hmac-md5'

vas_conf_libdefaults_default_etypes
-----------------------------------------
String to set value of default_etypes in [libdefaults] vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'arcfour-hmac-md5'

vas_conf_libdefaults_forwardable
--------------------------------
Boolean to set value of forwardable in [libdefaults] vas.conf. See VAS.CONF(5) for more info.

- *Default*: true

vas_conf_libdefaults_default_cc_name
--------------------------------
String to set where kerberos cache files should be stored (default on most systems is /tmp/krb5cc_${uid}).
Example: FILE:/new/path/krb5cc_${uid}

- *Default*: 'UNSET'

vas_conf_vas_auth_uid_check_limit
---------------------------------
Integer for uid-check-limit option in vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_vas_auth_allow_disconnected_auth
-----------------------------------------
Boolean to set value of allow-disconnected-auth option in [vas_auth] section of vas.conf. See VAS.CONF(5) for more info.
If set to 'UNSET' nothing will get printed.

- *Default*: 'UNSET'

vas_conf_vas_auth_expand_ac_groups
-----------------------------------------
Boolean to set value of expand-ac-groups option in [vas_auth] section of vas.conf. See VAS.CONF(5) for more info.
If set to 'UNSET' nothing will get printed.

- *Default*: 'UNSET'

vas_conf_libvas_vascache_ipc_timeout
------------------------------------
Integer for number of seconds to set value of vascache-ipc-timeout in [libvas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 15

vas_conf_libvas_use_server_referrals
------------------------------------
Boolean to set valut of use-server-referrals in [libvas] section of vas.conf. See VAS.CONF(5) for more info.
Set to 'USE_DEFAULTS' for automagically switching depending on running $vas_version. Also see $vas_conf_libvas_use_server_referrals_version_switch.

- *Default*: true

vas_conf_libvas_use_server_referrals_version_switch
---------------------------------------------------
String with version number to set use-server-referrals to false when $vas_conf_libvas_use_server_referrals is set to 'USE_DEFAULTS'.
Equal or higher version numbers will pull the trigger.

- *Default*: '4.1.0.21518'

vas_conf_libvas_auth_helper_timeout
-----------------------------------
Integer for number of seconds to set value of auth-helper-timeout in [libvas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 10

vas_conf_libvas_mscldap_timeout
-------------------------------
Integer to control the timeout when performing a MSCLDAP ping against AD Domain Controllers. See VAS.CONF(5) for more info.

- *Default*: 1

vas_conf_libvas_site_only_servers
---------------------------------
Boolean to set valut of site-only-servers in [libvas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: false

vas_conf_libvas_use_dns_srv
---------------------------
Boolean to set value of use-dns-srv in [libvas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: true

vas_conf_libvas_use_tcp_only
----------------------------
Boolean to set value of use-tcp-only in [libvas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: true

vas_conf_lowercase_names
------------------------
Boolean to set value of lowercase-names in [nss_vas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_conf_lowercase_homedirs
------------------------
Boolean to set value of lowercase-homedirs in [nss_vas] section of vas.conf. See VAS.CONF(5) for more info.

- *Default*: 'UNSET'

vas_config_path
---------------
Path to VAS config file.

- *Default*: '/etc/opt/quest/vas/vas.conf'

vas_config_owner
----------------
vas.conf's owner.

- *Default*: 'root'

vas_config_group
----------------
vas.conf's group.

- *Default*: 'root'

vas_config_mode
---------------
vas.conf's mode.

- *Default*: '0644'

vas_user_override_path
----------------------
Path to user-override file.

- *Default*: 'UNSET'

vas_user_override_owner
-----------------------
user-override's owner.

- *Default*: 'root'

vas_user_override_group
-----------------------
user-override's group.

- *Default*: 'root'

vas_user_override_mode
----------------------
user-override's mode.

- *Default*: '0644'

vas_users_allow_path
--------------------
Path to users.allow file.

- *Default*: 'UNSET'

vas_users_allow_owner
---------------------
users.allow's owner.

- *Default*: 'root'

vas_users_allow_group
---------------------
users.allow's group.

- *Default*: 'root'

vas_users_allow_mode
--------------------
users.allow's mode.

- *Default*: '0644'

vas_users_deny_path
-------------------
Path to users.deny file.

- *Default*: 'UNSET'

vas_users_deny_owner
--------------------
users.deny's owner.

- *Default*: 'root'

vas_users_deny_group
--------------------
users.deny's group.

- *Default*: 'root'

vas_users_deny_mode
-------------------
users.deny's mode.

- *Default*: '0644'

vas_group_override_path
-----------------------
Path to user-override file.

- *Default*: 'UNSET'

vas_group_override_owner
------------------------
group-override's owner.

- *Default*: 'root'

vas_group_override_group
------------------------
group-override's group.

- *Default*: 'root'

vas_group_override_mode
-----------------------
group-override's mode.

- *Default*: '0644'

vasjoin_logfile
---------------
Path to logfile used by AD join commando.

- *Default*: '/var/tmp/vasjoin.log'

solaris_vasclntpath
-------------------
*Solaris only* Path to Solaris vasclnt package.

- *Default*: 'UNSET'

solaris_vasyppath
-----------------
*Solaris only* Path to Solaris vasyp package.

- *Default*: 'UNSET'

solaris_vasgppath
-----------------
*Solaris only* Path to Solaris vasgp package.

- *Default*: 'UNSET'

solaris_adminpath
-----------------
*Solaris only* Path to Solaris package adminfile.

- *Default*: 'UNSET'

solaris_responsepattern
-----------------------
- *Default*: 'UNSET'

vastool_binary
--------------
Path to vastool binary to create symlink from

- *Default*: '/opt/quest/bin/vastool'

symlink_vastool_binary_target
-----------------------------
Path to where the symlink should be created

- *Default*: '/usr/bin/vastool'

symlink_vastool_binary
----------------------
Boolean for ensuring a symlink for vastool_binary to symlink_vastool_binary_target. This is useful since /opt/quest/bin is a non-standard location that is not in your $PATH.

- *Default*: false

license_files
-------------
Hash of license files

- *Default*: undef

join_domain_controllers
-----------------------
A string or an array with domain controllers to contact during the join process. Normally the servers for the domain will be automatically detected through DNS and LDAP lookups. By specifying this parameter vastool will contact the specified servers and only those servers during the join process. This can be useful if the machine being joined is not able to talk with all global Domain Controllers (e.g. due to firewalls). Note that this will have no effect after the join, where normal site discovery of servers will be made.

- *Default*: 'UNSET'
