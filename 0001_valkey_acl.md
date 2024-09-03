# ACLs in Redis/Valkey

Starting in Redis 6.0, ACLs were introduced allowing DBAs to set user passwords and permissions. By default Redis/Valkey uses a default user without a password and with full access to the Redis/Valkey database. There is a requirepass configuration parameter to configure a password for the default user.

ACLs were added for two main reasons:
1. Restrict user access.
2. Improve operational safety.

## Configure Redis/Valkey configuration file with ACL

Edit the configuration file to specify the "aclfile" directive to point to a file with a .acl extension name.
```
aclfile /path/to/users.acl
```

Create the file in the directory:
```
touch /path/to/users.acl
```

Comment out the `requirepass` directive since this directive is not compatible with aclfile directive.
```
# requirepass some-pass
```

## Create the user in the users.acl file
There are two ways to add users in Redis/Valkey, from the aclfile or from the console.

### Add/Edit user from the console
Start the `valkey-server`
```
/usr/bin/valkey-server /path/to/valkey.conf
```

Since there is no password configured, login to valkey using the cli:
```
/usr/bin/valkey-cli
```

Let's check the existing ACL setup for the default user
```
127.0.0.1:6379> acl getuser default
 1) "flags"
 2) 1) "on"
    2) "nopass"
    3) "sanitize-payload"
 3) "passwords"
 4) (empty array)
 5) "commands"
 6) "+@all"
 7) "keys"
 8) "~*"
 9) "channels"
10) "&*"
11) "selectors"
12) (empty array)
```

We can see the default user is set with `nopass` and the `passwords` section is empty.

Let's generate a password using `ACL GENPASS` and use it set the password for the default user using `ACL SETUSER`:
```
127.0.0.1:6379> acl genpass
"8165aa69c31942f1649097a76540cb1eff27c127b79c5061a63d43b267fe1c48"
127.0.0.1:6379> acl setuser default >8165aa69c31942f1649097a76540cb1eff27c127b79c5061a63d43b267fe1c48
OK
127.0.0.1:6379> acl getuser default
 1) "flags"
 2) 1) "on"
    2) "sanitize-payload"
 3) "passwords"
 4) 1) "c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b"
 5) "commands"
 6) "+@all"
 7) "keys"
 8) "~*"
 9) "channels"
10) "&*"
11) "selectors"
12) (empty array)
```
Prefix the plain-text password with the greater-than sign to signify that you are setting a password for this user.

Another option is to use a sha256 hashed password instead of a plain-text password.
```
codespace ➜ /workspace $ tr -dc A-Za-z0-9 </dev/urandom | head -c 24; echo
Vqg9rti1Z4eqV8UIlTFAn5II
codespace ➜ /workspace $ echo -n "Vqg9rti1Z4eqV8UIlTFAn5II" | sha256sum
63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0  -
codespace ➜ /workspace $ /usr/bin/valkey-cli 
127.0.0.1:6379> AUTH 8165aa69c31942f1649097a76540cb1eff27c127b79c5061a63d43b267fe1c48
OK
127.0.0.1:6379> acl setuser default #63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0
OK
127.0.0.1:6379> acl save
OK
127.0.0.1:6379> acl getuser default
 1) "flags"
 2) 1) "on"
    2) "sanitize-payload"
 3) "passwords"
 4) 1) "c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b"
    2) "63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0"
 5) "commands"
 6) "+@all"
 7) "keys"
 8) "~*"
 9) "channels"
10) "&*"
11) "selectors"
12) (empty array)
```
You will notice that we used `ACL SAVE` command, this saves all changes to the aclfile with passwords hashed with sha256 algorithm.
```
codespace ➜ /workspace $ cat conf/users.acl
user default on sanitize-payload #c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b #63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0 ~* &* +@all
```

### Add user from the aclfile

Get the hashed password.
```
codespace ➜ /workspace $ echo -n "bde70fc1e5fd05e2c8ed5c34111539275672a3a17465dcd54514c0a3a9151a69" | sha256sum
16a8174c2a35b7e67af2ab9bae220e77f971c041b50c1b4880fa902d16dc2770  -
```

Add the new user `valkeyuser` in the aclfile allowing only selected commands 
```
codespace ➜ /workspace $ cat conf/users.acl
user default on sanitize-payload #c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b #63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0 ~* &* +@all
user valkeyuser on sanitize-payload #16a8174c2a35b7e67af2ab9bae220e77f971c041b50c1b4880fa902d16dc2770 ~* resetchannels -@all +info +acl|whoami +ping
```

Let's login as default user and authenticate with it's password
```
codespace ➜ /workspace $ /usr/bin/valkey-cli 
127.0.0.1:6379> AUTH 8165aa69c31942f1649097a76540cb1eff27c127b79c5061a63d43b267fe1c48
OK
127.0.0.1:6379> acl list
1) "user default on sanitize-payload #c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b #63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0 ~* &* +@all"
```

Execute `acl load` to load the aclfile in to memory
```
127.0.0.1:6379> acl load
OK
127.0.0.1:6379> acl list
1) "user default on sanitize-payload #c12c00ad4fbc7d974d4521553b33762de8143dae6590adfea2bbe395ab02858b #63078c18d98caea6a47ca3cba932a813bca68c590c3b0de921b50bd5e0cb64c0 ~* &* +@all"
2) "user valkeyuser on sanitize-payload #16a8174c2a35b7e67af2ab9bae220e77f971c041b50c1b4880fa902d16dc2770 ~* resetchannels -@all +info +acl|whoami +ping"
```

Let's try to authenticate with the new `valkeyuser` and check if it works.
```
127.0.0.1:6379> AUTH valkeyuser bde70fc1e5fd05e2c8ed5c34111539275672a3a17465dcd54514c0a3a9151a69
OK
127.0.0.1:6379> info server
# Server
redis_version:7.2.4
server_name:valkey
valkey_version:7.2.5
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:5c23a165de2b45d3
redis_mode:standalone
os:Linux 6.10.0-linuxkit x86_64
arch_bits:64
monotonic_clock:POSIX clock_gettime
multiplexing_api:epoll
atomicvar_api:c11-builtin
gcc_version:9.4.0
process_id:9934
process_supervised:no
run_id:44ec29acf8612537deb52715d6fcb767b39d72f5
tcp_port:6379
server_time_usec:1725336450706747
uptime_in_seconds:9587
uptime_in_days:0
hz:10
configured_hz:10
lru_clock:14060418
executable:/usr/bin/valkey-server
config_file:/workspace/conf/valkey.conf
io_threads_active:0
listener0:name=tcp,bind=127.0.0.1,bind=-::1,port=6379
```

This new user has limited permissions, hence we can see an `ACL GETUSER` command will fail but a `WHOAMI` works.
```
127.0.0.1:6379> acl getuser valkeyuser
(error) NOPERM User valkeyuser has no permissions to run the 'acl|getuser' command
127.0.0.1:6379> acl whoami
"valkeyuser"
```