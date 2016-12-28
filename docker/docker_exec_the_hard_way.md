## `docker exec` the hard way

- Using tool `nsenter` we can enter any namespace.
- Install `nsenter` instreuctions [here](https://github.com/jpetazzo/nsenter#how-do-i-install-nsenter-with-this).
- Find `pid` of a running process:

```bash
docker inspect --format {{.State.Pid}} <container_name or container_id>
```

- Use `nsenter`

```bash
sudo nsenter --target <container pid> --uts --pid --net --mount --ipc
```


### Ref:

- Video: https://www.youtube.com/watch?v=qRxzrJwSW_4
- nsenter repo: https://github.com/jpetazzo/nsenter
- nsenter docs: http://man7.org/linux/man-pages/man1/nsenter.1.html
