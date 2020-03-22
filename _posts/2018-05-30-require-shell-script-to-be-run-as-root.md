---
title: Require Shell Script to be Run as Root
tags: shell bash
---

When creating a shell script to automate server configuration or maintenance, we often need to run the script with root access. Of course to do this is as simple as running our script using `sudo`. But what if we (or another user who use our script) forget to use `sudo`, then our script will generate errors, stop its execution, and potentially create a problem in our system.

Luckily, it's easy enough to prevent this from happening. We can check for current user id (the one running the script), and if it isn't equal to 0 (root user id), then we force the script execution to use `sudo`. We'll add this process in the beginning of our script.

### Using `id -u`
```bash
#!/usr/bin/env bash
# Check if the user is root, re-execute if not.
if [ "$(id -u)" -ne 0 ]; then
  exec sudo "$0" "$@"
  exit $?
fi
# ...
```

### Using `$EUID`
```bash
#!/usr/bin/env bash
# Check if the user is root, re-execute if not.
if [ "$EUID" -ne 0 ]; then
  exec sudo "$0" "$@"
  exit $?
fi
# ...
```

<br />

Both options will allow us to run our script using `sudo ./script.sh` or `./script.sh` and free us from the hassle of having to add sudo in front of all root-only commands within our script.

The difference between the first and second option is that the second will only work in `bash` (`$EUID` variable is only available in `bash`), while the first will work both in `sh` and `bash`.
