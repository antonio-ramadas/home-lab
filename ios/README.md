# iOS

It is possible to access the Raspberry Pi from iOS using the [iSH](https://github.com/ish-app/ish) app. The Linux distribution is [Alpine Linux](https://en.wikipedia.org/wiki/Alpine_Linux).

The app provides a [wiki](https://github.com/ish-app/ish/wiki) where [it details how to install `apk`](https://github.com/ish-app/ish/wiki/Installing-apk) if it is not present:

```bash
wget -qO- http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86/apk-tools-static-2.10.5-r1.apk | tar -xz sbin/apk.static && ./sbin/apk.static add apk-tools && rm sbin/apk.static && rmdir sbin 2> /dev/null
```

To be able to interact with the Raspberry Pi, it is necessary to add the OpenSSH client. Optionally, `man` is also missing and is useful while developing.

```bash
apk add mandoc man-pages openssh-client
```

## Limitations

- mDNS does not work (i.e., it is not possible to do `ssh pi@raspberry.local`)
