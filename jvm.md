# jvm

## GC Summary

```sh
jps # Get PID
jstat -gcutil <PID>
```

## Heap stats

```sh
jstat -gc `jps -l | grep play | awk {'print $1'}` 1000
```

Summary of Garbage Collection Statistics

- S0: Survivor space 0 utilization as a percentage of the space's current capacity.
- S1: Survivor space 1 utilization as a percentage of the space's current capacity.
- E: Eden space utilization as a percentage of the space's current capacity.
- O: Old space utilization as a percentage of the space's current capacity.
- P: Permanent space utilization as a percentage of the space's current capacity.

## Memory Pool Stats

```sh
jstat -gccapacity `jps -l | grep play | awk {'print $1'}` 1000 
```

minimum size (ms) and maximum size (mx) of each heap area, current size, and the number of GC performed for each area