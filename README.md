# RC2-DVM

Direct integration between a [RadioConsole2](https://github.com/W3AXL/RadioConsole2) instance and a [DVMProject FNE](https://github.com/DVMProject/dvmhost)!

## Building

`rc2-dvm` is built on .NET 8 and can be published for Windows and Linux (including Raspberry Pi 5 / ARM64).

### External Dependencies

`RC2-DVM` requires a copy of the `libvocoder` software vocoder library from the [dvmvocoder](https://github.com/DVMProject/dvmvocoder).

- On Windows, provide `libvocoder.dll` alongside `rc2-dvm.exe`.
- On Linux (including Raspberry Pi), provide `libvocoder.so` in a loader search path (for Docker, mount it to `/native/libvocoder.so`).

### Building RC2-DVM

Use the following steps to build and publish a single-file binary:

```console
$ git clone --recurse-submodules https://github.com/W3AXL/rc2-dvm
$ cd rc2-dvm
$ dotnet restore
$ dotnet build
$ dotnet publish -c Release -r linux-arm64 --self-contained true -p:PublishSingleFile=true "rc2-dvm/rc2-dvm.csproj"
```

Reference [`config.example.yml`](https://github.com/W3AXL/rc2-dvm/blob/main/rc2-dvm/config.example.yml) for information on configuring an `rc2-dvm` instance to communicate with your DVM FNE instance.

## Raspberry Pi 5 Docker Deployment (ARM64)

### 1) Prepare files on the Pi

Place the following files in `rc2-dvm/`:

- `config.yml` (copy and edit from `config.example.yml`)
- `libvocoder.so` (ARM64 build from dvmvocoder)
- `keyFile.yml` (optional, only if your config references it)

If you use `keyFile.yml`, mount it in compose (add a bind mount under `volumes`).

### 2) Build and run

From the repository root on the Pi:

```console
$ docker compose -f rc2-dvm/docker-compose.rpi.yml build
$ docker compose -f rc2-dvm/docker-compose.rpi.yml up -d
```

### 3) Check logs

```console
$ docker compose -f rc2-dvm/docker-compose.rpi.yml logs -f rc2-dvm
```

### Notes

- The compose file uses `network_mode: host` because RC2/FNE traffic is latency-sensitive and port-heavy.
- USB devices are mapped with `/dev/bus/usb` for external vocoder hardware access.
- `LD_LIBRARY_PATH` is set to `/native:/app` so `libvocoder.so` can be resolved.
