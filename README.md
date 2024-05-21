# Btrfs-bind is a btrfs snapshotter for rootless Containerd.

## Ensure your system

* kernel>=4.18

* containerd>=1.4

* Have a btrfs volume

* Install libbtrfs-devel

## How to use

#### Plug in

<details>
<summary>show detail</summary>
<p>

```shell
git clone <this repo>
cd btrfs-bind-snapshotter/cmd/btrfs-bind-snapshotter
go build
```

You can get btrfs-bind-snapshotter binary, then move this file to "/bin" .

Use it directly without systemd

```shell
btrfs-bind-snapshotter /run/containerd/containerd-btrfs-bind.sock /var/lib/containerd/io.containerd.snapshotter.v1.btrfs-bind/
```

If you have systemd, create this file.

```conf
#/lib/systemd/system/containerd-btrfs-bind.service
[Unit]
Description=containerd-btrfs-bind
PartOf=containerd.service

[Service]
ExecStart=btrfs-bind-snapshotter "/run/containerd/containerd-btrfs-bind.sock" "/var/lib/containerd/io.containerd.snapshotter.v1.btrfs-bind/"
ExecReload=/bin/kill -s HUP $MAINPID
RestartSec=2
Restart=always
Type=simple
KillMode=mixed

[Install]
WantedBy=default.target
```

Do not forget enable the service now.

```shell
systemctl daemon-reload
systemctl enable --now containerd-btrfs-bind
```

Modify Containerd configuration.

```toml
#/etc/containerd/config.toml
version=2
[plugins."io.containerd.grpc.v1.cri".containerd]
snapshotter="btrfs-bind"
[proxy_plugins]
[proxy_plugins."btrfs-bind"]
type = "snapshot"
address = "/run/containerd/containerd-btrfs-bind.sock"
```
</p>
</details>

#### Built in(no test)

<details>
<summary>show detail</summary>
<p>

Add this file.

```go
//$GOPATH/src/github.com/containerd/containerd/cmd/containerd/builtins_btrfsbind_linux.go
/*
   Copyright The containerd Authors.

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
*/

package main

import _ <<I donot know>>
```

Modify Containerd configuration.
```toml
#/etc/containerd/config.toml
version=2
[plugins."io.containerd.grpc.v1.cri".containerd]
snapshotter="btrfs-bind"
```
</p>
</details>
