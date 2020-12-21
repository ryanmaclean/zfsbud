# zfsbud
ZFS snapshotting, replicating & backup rotating convenience bash script.

## Introduction
This is a convenience script that helps to manage multiple ZFS operations within one command. The following operations are possible:
- Creation of a snapshot of one or multiple datasets
- Rotation (selective deletion) of older snapshots of one or multiple datasets while keeping 8 daily, 5 weekly, 13 monthly and 6 yearly snapshots
- Replication of one or multiple datasets to a local or remote location through ZFS send
- Smart/automatic handling of initial & consecutive send operations
- Dry run mode for output testing without actual changes being made
- Optional logging

## Usage
Here are some examples of usage.

### Snapshotting of a local dataset
`zfsbud.sh --create-snapshot pool/dataset1`
- `--create-snapshot|-c` creates a snapshot for each dataset with the following name: `pool/dataset@auto_YYYYMMDDHHMMSS`
- Snapshot name prefix (default: `auto_`) can be overridden with `--snapshot-prefix|-p <cute_name_>`

### Rotating/deleting of a dataset's snapshots made with this script
`zfsbud.sh --remove-old pool/dataset1`
- `--remove-old|-r` removes snapshots created by this script only
- If you created snapshots with a customized snapshot prefix, make sure to pass that prefix like so `--snapshot prefix|-p <cute_name_>`
- Keeps 8 daily, 5 weekly, 13 monthly and 6 yearly snapshots

### Initial sending of a dataset to a remote
`zfsbud.sh --send remote_pool_name --initial --rsh "ssh user@server -p22" pool/dataset1`
- `--initial|-i` will copy all snapshots over to the destination.
- The destination dataset must be non-existent.

### Consecutive sending of a dataset to a remote
`zfsbud.sh --send remote_pool_name --rsh "ssh user@server -p22" pool/dataset1`
- `--send|-s <remote_pool_name>` will figure out the last common snapshot between the source and destination and will send only the newer snapshots that are not present on the destination machine.
- This works with encrypted and unencrypted datasets.

### Create a snapshot of three datasets, rotate/remove old snapshots and send all changes to remote
`zfsbud.sh -c -r -s remote_pool_name -e "ssh user@server -p22" pool/dataset1 pool/dataset2 pool/dataset3`

### Logging
- To log, add `--log|-l` or `--log-path|-L </path/to/file>`
- To add verbosity, add `--verbose|-v`.

### Dry run
To see the output of what would happen without making actual changes, add `--dry-run|-d`. This is highly recommended before any new usage of this script.

### Help
Use `--help|-h` to show help.
```
Usage: zfsbud [OPTION]... SOURCE_POOL/DATASET [SOURCE_POOL/DATASET2...]

 -s, --send <destination_pool_name>   send source dataset incrementally to destination
 -i, --initial                        initially clone source dataset to destination (requires --send)
 -e, --rsh <'ssh user@server -p22'>   send to remote destination by providing ssh connection string (requires --send)
 -c, --create-snapshot                create a timestamped snapshot on source
 -r, --remove-old                     remove all but the most recent, the last common (if sending), 8 daily, 5 weekly, 13 monthly and 6 yearly source snapshots
 -d, --dry-run                        show output without making actual changes
 -p, --snapshot-prefix <prefix>       use a snapshot prefix other than '_auto'
 -v, --verbose                        increase verbosity
 -l, --log                            log to user's home directory
 -L, --log-path </path/to/file>       provide path to log file (implies --log)
 -h, --help                           show this help
```

## Caution
This script does things. Use with caution!.

## Credit
Created and maintained by Pawel Ginalski (https://gbyte.dev).
