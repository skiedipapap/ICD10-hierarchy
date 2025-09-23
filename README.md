```
    __                 __         
   / /___ _____ ____  / /__  _____
  / / __ `/ __ `/ _ \/ / _ \/ ___/
 / / /_/ / /_/ /  __/ /  __/ /    
/_/\__,_/\__, /\___/_/\___/_/     
        /____/                    
```

**laggler - network lag simulator for testing**

## why

test your app under poor network conditions:

- slow 3g (2mbps down, 500ms latency)
- packet loss (5% random drops)
- bandwidth limits
- latency spikes

## install

```bash
brew install laggler
# or
apt install laggler
```

## usage

```bash
# simulate slow connection
laggler start --preset slow-3g

# custom settings
laggler start --latency 300ms --bandwidth 1mbps --loss 10%

# apply to specific process
laggler attach --pid 1234 --latency 500ms

# stop simulation
laggler stop
```

## presets

| preset | latency | bandwidth | loss |
|--------|---------|-----------|------|
| slow-3g | 500ms | 2mbps | 2% |
| fast-3g | 150ms | 8mbps | 0% |
| 4g | 50ms | 50mbps | 0% |
| satellite | 800ms | 5mbps | 5% |
| dialup | 300ms | 56kbps | 10% |

## advanced

### jitter

add random latency variation:

```bash
laggler start --latency 100ms --jitter 50ms
```

### throttle by ip

```bash
laggler rule add --dest 192.168.1.0/24 --bandwidth 100kbps
```

### time-based profiles

```bash
# slow between 9am-5pm
laggler schedule --time "09:00-17:00" --preset slow-3g
```

## integration

### docker

```bash
docker run --cap-add=NET_ADMIN \
  -e LAGGLER_PRESET=slow-3g \
  myapp:latest
```

### ci/cd

```yaml
# .github/workflows/test.yml
- name: simulate bad network
  run: |
    laggler start --preset slow-3g
    npm test
    laggler stop
```

## how it works

uses **netfilter-hooks** kernel module to intercept packets

delay queue managed by **tc-queue** (traffic control)

packet dropping via **iptables-rand** random match

## supported platforms

- linux (kernel 4.0+)
- macos (via pf firewall)
- windows (wip, unstable)

## limitations

- requires root/admin privileges
- doesn't affect localhost traffic
- udp packet loss estimation not perfect

## alternatives

- **toxiproxy** (proxy-based, requires code changes)
- **tc** (linux only, complex syntax)
- **comcast** (macos only, abandoned)

---

GPL-3.0 • [github](https://github.com/nettools/laggler) • [docs](https://laggler.io/docs)
