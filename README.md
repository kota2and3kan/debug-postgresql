# Container image for testing PostgreSQL debugging

## Note

- For testing purposes only. Not for production.
- To run the `gdb` command in the container, you must set the `--privileged` option to `docker run` command.

## Usage

### Start a PostgreSQL server

1. Run a PostgreSQL container.

   ```console
   docker run -d --name debug-postgresql --privileged ghcr.io/kota2and3kan/debug-postgresql:17 sleep inf
   ```

1. Run bash in the PostgreSQL container.

   ```console
   docker exec -it debug-postgresql bash
   ```

1. Switch user to the `postgres` user.

   ```console
   su - postgres
   ```

1. Start a PostgreSQL server.

   ```console
   pg_ctl start
   ```

### Run a `gdb` command

After running a PostgreSQL server, for example, you can attach a process of PostgreSQL as follows:

1. Run bash in the PostgreSQL container.

   ```console
   docker exec -it debug-postgresql bash
   ```

1. Check PID of PostgreSQL processes.

   ```console
   ps -ef
   ```

1. Run the `gdb` command to attach a process of PostgreSQL.

   ```console
   gdb --pid <PID> -q
   ```

### Remove the PostgreSQL container

You can remove the PostgreSQL container as follows:

```console
docker rm $(docker kill debug-postgresql)
```

### Example

- Start a PostgreSQL container.

  ```console
  $ docker run -d --name debug-postgresql --privileged ghcr.io/kota2and3kan/debug-postgresql:17 sleep inf
  5db345c66d24991efadcdb3cdfb9be5de2a1b9f878f7385b1523ec2fc659d903
  ```

- Start a PostgreSQL server in the container.

  ```console
  $ docker exec -it debug-postgresql bash
  root@5db345c66d24:/#
  root@5db345c66d24:/# su - postgres
  postgres@5db345c66d24:~$
  postgres@5db345c66d24:~$ pg_ctl start
  waiting for server to start....2024-10-07 08:53:59.172 UTC [23] LOG:  00000: redirecting log output to logging collector process
  2024-10-07 08:53:59.172 UTC [23] HINT:  Future log output will appear in directory "log".
  2024-10-07 08:53:59.172 UTC [23] LOCATION:  SysLogger_Start, syslogger.c:730
   done
  server started
  postgres@5db345c66d24:~$
  ```

- Run a `gdb` command.

  ```console
  $ docker exec -it debug-postgresql bash
  root@5db345c66d24:/#
  root@5db345c66d24:/# ps -ef
  UID        PID  PPID  C STIME TTY          TIME CMD
  root         1     0  0 08:53 ?        00:00:00 sleep inf
  postgres    23     1  0 08:53 ?        00:00:00 /usr/local/pgsql/bin/postgres
  postgres    24    23  0 08:53 ?        00:00:00 postgres: logger
  postgres    25    23  0 08:53 ?        00:00:00 postgres: checkpointer
  postgres    26    23  0 08:53 ?        00:00:00 postgres: background writer
  postgres    28    23  0 08:53 ?        00:00:00 postgres: walwriter
  postgres    29    23  0 08:53 ?        00:00:00 postgres: autovacuum launcher
  postgres    30    23  0 08:53 ?        00:00:00 postgres: logical replication launcher
  root        32     0  0 08:54 pts/1    00:00:00 bash
  root        40    32  0 08:54 pts/1    00:00:00 ps -ef
  root@5db345c66d24:/#
  Attaching to process 25
  Reading symbols from /usr/local/pgsql/bin/postgres...
  Reading symbols from /lib/x86_64-linux-gnu/libssl.so.3...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libssl.so.3)
  Reading symbols from /lib/x86_64-linux-gnu/libcrypto.so.3...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libcrypto.so.3)
  Reading symbols from /lib/x86_64-linux-gnu/libz.so.1...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libz.so.1)
  Reading symbols from /lib/x86_64-linux-gnu/libm.so.6...
  Reading symbols from /usr/lib/debug/.build-id/a5/08ec5d8bf12fb7fd08204e0f87518e5cd0b102.debug...
  Reading symbols from /lib/x86_64-linux-gnu/libicui18n.so.70...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libicui18n.so.70)
  Reading symbols from /lib/x86_64-linux-gnu/libicuuc.so.70...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libicuuc.so.70)
  Reading symbols from /lib/x86_64-linux-gnu/libc.so.6...
  Reading symbols from /usr/lib/debug/.build-id/49/0fef8403240c91833978d494d39e537409b92e.debug...
  Reading symbols from /lib64/ld-linux-x86-64.so.2...
  Reading symbols from /usr/lib/debug/.build-id/41/86944c50f8a32b47d74931e3f512b811813b64.debug...
  Reading symbols from /lib/x86_64-linux-gnu/libstdc++.so.6...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libstdc++.so.6)
  Reading symbols from /lib/x86_64-linux-gnu/libgcc_s.so.1...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libgcc_s.so.1)
  Reading symbols from /lib/x86_64-linux-gnu/libicudata.so.70...
  (No debugging symbols found in /lib/x86_64-linux-gnu/libicudata.so.70)
  [Thread debugging using libthread_db enabled]
  Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
  0x00007fc8a1709dea in epoll_wait (epfd=5, events=0x557b450a2e58, maxevents=1, timeout=231745) at ../sysdeps/unix/sysv/linux/epoll_wait.c:30
  30      ../sysdeps/unix/sysv/linux/epoll_wait.c: No such file or directory.
  (gdb) bt
  #0  0x00007fc8a1709dea in epoll_wait (epfd=5, events=0x557b450a2e58, maxevents=1, timeout=231745) at ../sysdeps/unix/sysv/linux/epoll_wait.c:30
  #1  0x0000557b42dc02c3 in WaitEventSetWaitBlock (set=0x557b450a2df0, cur_timeout=231745, occurred_events=0x7ffd87650e50, nevents=1) at latch.c:1570
  #2  0x0000557b42dc01da in WaitEventSetWait (set=0x557b450a2df0, timeout=300000, occurred_events=0x7ffd87650e50, nevents=1, wait_event_info=83886084) at latch.c:1516
  #3  0x0000557b42dbf636 in WaitLatch (latch=0x7fc89f4509d4, wakeEvents=41, timeout=300000, wait_event_info=83886084) at latch.c:538
  #4  0x0000557b42d1c70a in CheckpointerMain (startup_data=0x0, startup_data_len=0) at checkpointer.c:547
  #5  0x0000557b42d1daad in postmaster_child_launch (child_type=B_CHECKPOINTER, startup_data=0x0, startup_data_len=0, client_sock=0x0) at launch_backend.c:277
  #6  0x0000557b42d24113 in StartChildProcess (type=B_CHECKPOINTER) at postmaster.c:3928
  #7  0x0000557b42d206c4 in PostmasterMain (argc=1, argv=0x557b450a2760) at postmaster.c:1357
  #8  0x0000557b42be288e in main (argc=1, argv=0x557b450a2760) at main.c:197
  (gdb) info local
  sc_ret = -4
  sc_ret = <optimized out>
  (gdb) quit
  A debugging session is active.
  
          Inferior 1 [process 25] will be detached.
  
  Quit anyway? (y or n) y
  Detaching from program: /usr/local/pgsql/bin/postgres, process 25
  [Inferior 1 (process 25) detached]
  root@5db345c66d24:/#
  ```

- Remove the PostgreSQL container.

  ```console
  $ docker rm $(docker kill debug-postgresql)
  debug-postgresql
  ```

## Other information

You can see the environment variables related to PostgreSQL running in this container image as follows:

```console
docker run -t --rm ghcr.io/kota2and3kan/debug-postgresql:17 su - postgres -c pg_config
```

## License

Please refer to the [LICENSE](https://github.com/kota2and3kan/debug-postgresql/blob/main/LICENSE).
