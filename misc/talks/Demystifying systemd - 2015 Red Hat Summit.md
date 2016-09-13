# Demystifying systemd - 2015 Red Hat Summit

Ben Breard — Senior Solutions Architect, Red Hat Lennart Poettering — Red Hat

Red Hat Enterprise Linux 7 brings a modern approach to many elements of the Linux operating system. One of the most significant of these updates is the adoption of systemd, which gives admins and users a host of exciting tools and functionality.

In this session, you'll learn how to get the most out of systemd in Red Hat Enterprise Linux 7, including:
- How init commands translate in systemd.
- Converting legacy init scripts.
- Customizing service unit files.
- Resource allocation via cgroups.


### Systemd Units locations

- Maintainer: `/usr/lib/systemd/system`
- Administrator: `/etc/systemd/system`
- Non-persistent, runtime: `/run/systemd/system`

Note: unit files in `/etc` take precedence over `/usr`

- Don't edit the unit files under `/usr/lib/systemd/system` these files are created by the rpm maintainer. To tweak them in `/etc/systemd/system`.

### Managing Services: Status

- List loaded services:

    ```bsah
    systemctl -t service
    ```

- List installed services:

    ```bash
    systemctl list-unit-files -t service
    ```

- Check for services in failed state:

    ```bash
    systemctl --state failed
    ```

### Systemd GUI:

- via Cockpit

### Sockets

- Define socket files, systemd will listen on that port and start service when there is a request on that port.
- This can also be used to start container when request comes in.
- But has to be used with only services that takes very less time to come up. (less time ~ time un-noticable to client making request)
- e.g. Cockpit service runs like this, cockpit is started only when request comes up.


### What's Available?

- List unit's properties:

    ```
    systemctl show --all httpd
    ```

- Query a single property

    ```
    $ systemctl show -p Restart httpd
    Restart=no
    ```
- Helpful man files: `systemd.exec` and `systemd.service`

    - `Restart`, `Nice`, `CPUAffinity`, `OOMScoreAdjust`, `LimitNOFILE`, etc.

### Customizing Units: Drop-ins

- Create dir

    ```mkdir /etc/systemd/system/[name.type.d]/

- Create drop-in

    - A file with overrides

- Notify systemd of the changes

    - `systemctl daemon-reload`

- To see what's been altered on the system

    ```
    systemd-delta
    ```

### Resource management

- c-group list -> `systemd-cgls`
- c-group top -> `systemd-cgtop` Performance metrics


Links:

- Video: https://www.youtube.com/watch?v=S9YmaNuvw5U
- Slides: https://www.redhat.com/en/summit/2015/presentations
- Video Desc: https://www.redhat.com/en/about/videos/demystifying-systemd-summit-2015
