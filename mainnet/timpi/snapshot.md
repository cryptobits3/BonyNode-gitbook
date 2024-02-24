# Snapshot

### Downloading the snapshot

Downloading the snapshot depends on your internet speed, which will determine how long this process takes. I’m on a 600 Mbps connection with an NVMe drive, and it typically takes around 15 minutes. The snapshot file is approximately 10 GB in size, but it’s downloaded faster than syncing the blockchain. I’ll pause the video during this part of the process.

```
cd $HOME/.Neutaro/
mv data data-old
wget http://109.199.106.233/snapshotNeutaro.tar.lz4
lz4 -d snapshotNeutaro.tar.lz4
tar -xf snapshotNeutaro.tar
```
