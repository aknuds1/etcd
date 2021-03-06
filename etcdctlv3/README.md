etcdctl
========

## Commands

### PUT [options] \<key\> \<value\>

PUT assigns the specified value with the specified key. If key already holds a value, it is overwritten.

#### Options

- lease -- lease ID (in hexadecimal) to attach to the key.

#### Return value

Simple reply

- OK if PUT executed correctly. Exit code is zero. 

- Error string if PUT failed. Exit code is non-zero.

TODO: probably json and binary encoded proto

#### Examples

``` bash
./etcdctl PUT foo bar --lease=0x1234abcd
OK
./etcdctl range foo
bar
```

#### Notes

If \<value\> isn't given as command line argument, this command tries to read the value from standard input.

When \<value\> begins with '-', \<value\> is interpreted as a flag.
Insert '--' for workaround:

``` bash
./etcdctl put <key> -- <value>
./etcdctl put -- <key> <value>
```

### GET [options] \<key\> [range_end]

GET gets the key or a range of keys [key, range_end) if `range-end` is given.

#### Options

- hex -- print out key and value as hex encode string

- limit -- maximum number of results

- order -- order of results; ASCEND or DESCEND

- sort-by -- sort target; CREATE, KEY, MODIFY, VALUE, or VERSION

TODO: add consistency, from, prefix

#### Return value

Simple reply

- \<key\>\r\n\<value\>\r\n\<next_key\>\r\n\<next_value\>...

- Error string if GET failed. Exit code is non-zero.

TODO: probably json and binary encoded proto

#### Examples

``` bash
./etcdctl get foo
foo
bar
```

#### Notes

If any key or value contains non-printable characters or control characters, the output in text format (e.g. simple reply or JSON reply) might be ambiguous.
Adding `--hex` to print key or value as hex encode string in text format can resolve this issue.

### DEL [options] \<key\> [range_end]

Removes the specified key or range of keys [key, range_end) if `range-end` is given.

#### Options

TODO: --prefix, --from

#### Return value

Simple reply

- The number of keys that were removed in decimal if DEL executed correctly. Exit code is zero. 

- Error string if DEL failed. Exit code is non-zero.

TODO: probably json and binary encoded proto

#### Examples

``` bash
./etcdctl put foo bar
OK
./etcdctl del foo
1
./etcdctl range foo
```

### WATCH [options] [key or prefix]

Watch watches events stream on keys or prefixes. The watch command runs until it encounters an error or is terminated by the user.

#### Options

- hex -- print out key and value as hex encode string

- i -- begins an interactive watch session

- prefix -- watch on a prefix if prefix is set.

- rev -- the revision to start watching. Specifying a revision is useful for observing past events.

#### Return value

Simple reply

- \<event\>\<key\>\r\n\<value\>\r\n\<event\>\<next_key\>\r\n\<next_value\>...

- Additional error string if WATCH failed. Exit code is non-zero.

TODO: probably json and binary encoded proto

#### Examples

``` bash
./etcdctl watch foo
PUT
foo
bar
```

#### Notes

TODO: doc interactive mode

## Utility Commands

### MAKE-MIRROR [options] \<destination\>

[make-mirror][mirror] mirrors a key prefix in an etcd cluster to a destination etcd cluster.

#### Options

- dest-cacert -- TLS certificate authority file for destination cluster

- dest-cert -- TLS certificate file for destination cluster

- dest-key -- TLS key file for destination cluster

- prefix -- The key-value prefix to mirror

#### Return value

Simple reply

- The approximate total number of keys transferred to the destination cluster, updated every 30 seconds.

- Error string if mirroring failed. Exit code is non-zero.

#### Examples

```
./etcdctl make-mirror mirror.example.com:2379
10
18
```

[mirror]: ./doc/mirror_maker.md
