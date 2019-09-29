# sh
shell script utility (bash)
```bash
# wait for any process
function await() { while ps $@ > /dev/null; do sleep 10s; done }

# get filesize
alias filesize='du -b'

# make nProc worker
function mkworkers() {
  if (( 0 == $# )); then
    echo 'Usage: mkcpuq fifoname [nThreads [command+args]]'
    return 1
  fi
  NAME=$1
  N_CORES=$2
  N_CORES=${N_CORES:-$(nproc)}
  mkfifo /tmp/$NAME
  trap rm -fv /tmp/$NAME EXIT
  if (( 2 < $# )); then
    xargs -d '\n' -n1 -P$N_CORES "${@:2}" < /tmp/$NAME
  else
    xargs -d '\n' -n1 -P$N_CORES bash -c < /tmp/$NAME
  fi
}
# submit to worker
function submit() {
  NAME=$1
  if [ -p /tmp/$NAME ]; then
    for F in "${@:1}"; do readlink -f $F; done > /tmp/$NAME
  else
    echo "Queue not found: '$NAME'"
    return 1
  fi
}

```
