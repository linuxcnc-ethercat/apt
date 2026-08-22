# linuxcnc-ethercat apt repository

Debian apt repository for the [linuxcnc-ethercat](https://github.com/linuxcnc-ethercat/linuxcnc-ethercat)
and [ethercat](https://github.com/linuxcnc-ethercat/ethercat) projects, served via GitHub Pages.

URL: <https://linuxcnc-ethercat.github.io/apt/>

## Supported distributions

| Codename | Debian | Architectures |
| -------- | ------ | ------------- |
| bullseye | 11     | amd64, arm64  |
| bookworm | 12     | amd64, arm64  |
| trixie   | 13     | amd64, arm64  |

## Usage

```sh
# 1. Fetch the archive signing key
sudo curl -fsSL https://linuxcnc-ethercat.github.io/apt/linuxcnc-ethercat-apt.gpg \
    -o /usr/share/keyrings/linuxcnc-ethercat-apt.gpg

# 2. Detect the codename and add the apt source
. /etc/os-release
case "$VERSION_CODENAME" in
    bullseye|bookworm|trixie) ;;
    *) echo "Unsupported codename: $VERSION_CODENAME (supported: bullseye, bookworm, trixie)" >&2; exit 1 ;;
esac
echo "deb [signed-by=/usr/share/keyrings/linuxcnc-ethercat-apt.gpg] \
https://linuxcnc-ethercat.github.io/apt/ $VERSION_CODENAME main" \
    | sudo tee /etc/apt/sources.list.d/linuxcnc-ethercat.list

# 3. Update and install
sudo apt-get update
sudo apt-get install linuxcnc-ethercat ethercat-master
```

> [!WARNING]
> If you recently ran `apt upgrade` and a new kernel was installed but
> you have not rebooted yet, **reboot first**. The `ethercat` DKMS
> module is built only for the running kernel, so installing mid-update
> leaves you without a working module after your next reboot. If this
> already happened, run `sudo dkms autoinstall` and
> `sudo systemctl restart ethercat`.

## Signing key

- Fingerprint: `480E E771 AEFB A7BE 1FBE  D258 BC33 B93B F271 4DA7`
- Identity: `LinuxCNC-EtherCAT APT Archive Key <apt@linuxcnc-ethercat.github.io>`

## Maintenance

Releases are ingested automatically: each source repo's release workflow
fires a `repository_dispatch` of type `ingest-release` against this repo,
which runs `.github/workflows/ingest.yml` to pull the tag's .deb assets
and add them to the pool via `reprepro`.

Manual ingestion is also available via **Actions -> Ingest release ->
Run workflow**.
