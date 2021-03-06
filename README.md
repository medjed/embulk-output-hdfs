# Hdfs file output plugin for Embulk

[![Build Status](https://travis-ci.org/civitaspo/embulk-output-hdfs.svg)](https://travis-ci.org/civitaspo/embulk-output-hdfs)
[![Coverage Status](https://coveralls.io/repos/github/civitaspo/embulk-output-hdfs/badge.svg?branch=master)](https://coveralls.io/github/civitaspo/embulk-output-hdfs?branch=master)

A File Output Plugin for Embulk to write HDFS.

## Overview

* **Plugin type**: file output
* **Load all or nothing**: yes
* **Resume supported**: no
* **Cleanup supported**: no

## Configuration

- **config_files** list of paths to Hadoop's configuration files (array of strings, default: `[]`)
- **config** overwrites configuration parameters (hash, default: `{}`)
- **path_prefix** prefix of target files (string, required)
- **file_ext** suffix of target files (string, required)
- **sequence_format** format for sequence part of target files (string, default: `'%03d.%02d.'`)
- **rewind_seconds** When you use Date format in path_prefix property(like `/tmp/embulk/%Y-%m-%d/out`), the format is interpreted by using the time which is Now minus this property. (int, default: `0`)
- **overwrite** overwrite files when the same filenames already exists (boolean, default: `false`)
    - *caution*: even if this property is `true`, this does not mean ensuring the idempotence. if you want to ensure the idempotence, you need the procedures to remove output files after or before running. 
- **doas** username which access to Hdfs (string, default: executed user)
- **delete_in_advance** delete files and directories having `path_prefix` in advance (enum, default: `NONE`)
    - `NONE`: do nothing
    - `FILE_ONLY`: delete files
    - `RECURSIVE`: delete files and directories

## CAUTION
If you use `hadoop` user (hdfs admin user) as `doas`, and if `delete_in_advance` is `RECURSIVE`,
`embulk-output-hdfs` can delete any files and directories you indicate as `path_prefix`,
this means `embulk-output-hdfs` can destroy your hdfs.
So, please be careful when you use `delete_in_advance` option and `doas` option ...

## Example

```yaml
out:
  type: hdfs
  config_files:
    - /etc/hadoop/conf/core-site.xml
    - /etc/hadoop/conf/hdfs-site.xml
  config:
    fs.defaultFS: 'hdfs://hdp-nn1:8020'
    fs.hdfs.impl: 'org.apache.hadoop.hdfs.DistributedFileSystem'
    fs.file.impl: 'org.apache.hadoop.fs.LocalFileSystem'
  path_prefix: '/tmp/embulk/hdfs_output/%Y-%m-%d/out'
  file_ext: 'txt'
  overwrite: true
  formatter:
    type: csv
    encoding: UTF-8
```


## Build

```
$ ./gradlew gem
```

## Development

```
$ ./gradlew classpath
$ bundle exec embulk run -I lib example.yml
```
