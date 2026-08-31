# Remote environments

Remote environments run outside the local desktop app or web browser. They
communicate with clients over WebSockets, HTTP, or relayed protocols.

## Connection routes

Clients can reach remote environments through several routes:

- Direct local network (LAN) connections
- Tailscale private network mesh
- T3 Connect managed relay tunnels
- SSH port-forwarded connections
- Peer-to-peer encrypted connections over Hyperswarm DHT

## Trust and boundaries

Remote environments execute agent code and access files on their host machines.
Clients verify environment identity during pairing. See
[T3 Connect trust boundary](./t3-connect.md).

SSH can launch a server as well as forward a port. Desktop main owns that
lifecycle because it can spawn SSH and handle authentication prompts. The
renderer uses the forwarded endpoint through the shared connection runtime.
[SSH cleanup](../../packages/ssh/src/tunnel.ts) stops a remote server only if the
launcher owns it; a server it discovered already running must survive a client
disconnect. Reconnection restores the forward before opening the application
transport.

Peer-to-peer connections dial an environment by DHT public key over Hyperswarm.
Desktop and mobile platforms run a local dialer gateway that maps the encrypted
Noise stream to loopback URLs, allowing ordinary bearer authorization over the tunnel.

Remote servers can outlive several client releases. Clients must use advertised
capabilities and handle their absence, rather than assume their own version
describes the server. Process replacement belongs to the launcher's
[update protocol](./server-updates.md); the connection runtime handles the
resulting disconnect.
