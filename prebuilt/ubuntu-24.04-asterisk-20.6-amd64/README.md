# chan_dongle.so for Ubuntu 24.04 / Asterisk 20.6 (amd64)

Prebuilt module, the one running on our GSM gateway with a Huawei E303s-1 (stick firmware 21.158.13.00.864).

| | |
|---|---|
| OS | Ubuntu 24.04.5 LTS, x86_64, glibc 2.39, gcc 13.3.0 |
| Asterisk | Ubuntu package `asterisk 1:20.6.0~dfsg+~cs6.13.40431414-2build5` |
| Source | upstream `wdoekes/asterisk-chan-dongle` @ `7db8e75` + the `AT+CSSN=0,0` patch from this fork |
| md5 | `2e6f67123d7a2fec619e38d92d50baa9` |

## Why the patch

On the E303 the modem sends `+CSSI: 1` between `^CONF:1` and `^CONN:1,0`. chan_dongle reads exactly
8 bytes for `+CSSI`, the leftover `\r\n` desyncs the reader and `^CONN` is lost: the call stays in
`dialing` and is hung up after the originate timeout. `AT+CSSN=0,0` turns the notifications off.

## Install

```bash
sudo install -m 644 chan_dongle.so /usr/lib/x86_64-linux-gnu/asterisk/modules/
sudo systemctl restart asterisk
sudo asterisk -rx "dongle show devices"
```

## Build it yourself

```bash
sudo apt install asterisk asterisk-dev build-essential autoconf automake
./bootstrap
./configure --with-astversion=20.6 --with-asterisk=/usr/include DESTDIR=/usr/lib/x86_64-linux-gnu/asterisk/modules
make && sudo make install
```

`configure` fails without the explicit `DESTDIR`.
