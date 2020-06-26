# dweb-discovery

[![build status](https://travis-ci.org/karissa/dweb-discovery.svg?branch=master)](http://travis-ci.org/karissa/dweb-discovery)

Join the p2p swarm for [ddatabase][core], [dwebfs][drive], and [dappdb][db] feeds. Uses
[dweb-discovery-swarm][swarm] under the hood.

```
npm install dweb-discovery
```

## Usage

Run the following code in two different places and they will replicate the contents of the given `ARCHIVE_KEY`.

```js
var dwebfs = require('dwebfs')
var swarm = require('dweb-discovery')

var archive = dwebfs('./database', 'ARCHIVE_KEY')
var sw = swarm(archive)
sw.on('connection', function (peer, type) {
  console.log('got', peer, type) 
  console.log('connected to', sw.connections.length, 'peers')
  peer.on('close', function () {
    console.log('peer disconnected')
  })
})
```

Will use `dweb-discovery-swarm` to attempt to connect peers. Uses `datland-swarm-defaults` for peer introduction defaults on the server side, which can be overwritten (see below).

The module can also create and join a swarm for a ddatabase feed:

```js
var ddatabase = require('ddatabase')
var swarm = require('dweb-discovery')

var feed = ddatabase('/feed')
var sw = swarm(feed)
```

The module can also create and join a swarm for a dappdb feed:

```js
var dappdb = require('dappdb')
var swarm = require('dweb-discovery')

var db = dappdb('/feed', 'ARCHIVE_KEY')
db.on('ready', function() {
  var sw = swarm(db)
})
```

A dappdb database must be ready before attempting to connect to the swarm. When `download` is enabled the swarm will automatically `db.authorize()` all connecting peers.

## API

### `var sw = swarm(archive, opts)`

Join the p2p swarm for the given feed. The return object, `sw`, is an event emitter that will emit a `peer` event with the peer information when a peer is found.

### sw.connections

Get the list of currently active connections.

### sw.close()

Exit the swarm

##### Options

  * `stream`: function, replication stream for connection. Default is `archive.replicate({live, upload, download})`.
  * `upload`: bool, upload data to the other peer?
  * `download`: bool, download data from the other peer?
  * `port`: port for discovery swarm
  * `utp`: use utp in discovery swarm
  * `tcp`: use tcp in discovery swarm

Defaults from datland-swarm-defaults can also be overwritten:

  * `dns.server`: DNS server
  * `dns.domain`: DNS domain
  * `dht.bootstrap`: distributed hash table bootstrapping nodes

## See Also
- [mafintosh/ddatabase][core]
- [mafintosh/dwebfs][drive]
- [mafintosh/dappdb][db]
- [mafintosh/dweb-discovery-swarm][swarm]

## License
ISC

[core]: https://github.com/distributedweb/ddatabase
[drive]: https://github.com/distributedweb/dwebfs
[db]: https://github.com/distributedweb/dappdb
[swarm]: https://github.com/distributedweb/dweb-discovery-swarm
