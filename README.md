# What

Docker image for a Spotify Connect endpoint.

This is based on [LibreSpot](https://github.com/librespot-org/librespot).

This is useful in the following scenarios:

 1. you are a hobbyist and you want to turn a small appliance connected to speakers into a Spotify Connect receiver (typically a raspberry pi) 
 1. that's it :-)

## Image features

 * multi-architecture:
    * [x] linux/amd64
    * [x] linux/arm64
    * [x] linux/arm/v7
    * [x] linux/s390x
    * [x] linux/ppc64le
    * [ ] linux/arm/v6 (may build, but unsupported right now)
    * [ ] linux/386 (may build, but unsupported right now)
 * hardened:
    * [x] image runs read-only
    * [x] image runs with no capabilities
    * [x] process runs as a non-root user, disabled login, no shell
 * lightweight
    * [x] based on our slim [Debian buster version](https://github.com/dubo-dubon-duponey/docker-debian)
    * [x] simple entrypoint script
    * [ ] multi-stage build ~~with no installed dependencies~~ dependent on the following for the runtime image:
        * libpulse0
        * libasound2
 * observable
    * [x] healthcheck
    * [x] log to stdout
    * [ ] ~~prometheus endpoint~~ not applicable

## Run

The following is the most straight-forward example, using host networking:

```bash
docker run -d --rm \
    --name "spot" \
    --env "NAME=Super Name For Your Spotify Connect Endpoint" \
    --volume /tmp \
    --group-add audio \
    --device /dev/snd \
    --net host \
    --cap-drop ALL \
    --read-only \
    dubodubonduponey/librespot
```

## Notes

### Networking

You need to run this in `host` or `mac(or ip)vlan` networking (because of mDNS).

### Additional arguments

Any additional arguments when running the image will get fed to the `librespot` binary.

This is specifically relevant if you need to select a different alsa device, card or mixer, or use another librespot option.

Here is an example:
```bash
docker run -d --rm \
    --name "spot" \
    --env "NAME=Super Name For Your Spotify Connect Endpoint" \
    --volume /tmp \
    --group-add audio \
    --device /dev/snd \
    --net host \
    --cap-drop ALL \
    --read-only \
    dubodubonduponey/librespot \
    --device default:CARD=Mojo \
    --enable-volume-normalisation \
    -v
```

For a reference of all librespot options, try:
```bash
docker run --rm \
    dubodubonduponey/librespot \
    --help
```

### Custom configuration

You may specify the following environment variables at runtime:

 * `NAME` (eg: `Totale Croquette`) controls the "name" under which your endpoint will appear in Spotify

You can also tweak the following for control over which internal ports are being used:

 * `PORT` (eg: `10042`) controls the port used by the http command endpoint

Of course using any privileged port for that would require `CAP_NET_BIND_SERVICE` and a `--user=root` (not recommended...).

## Moar?

See [DEVELOP.md](DEVELOP.md)
