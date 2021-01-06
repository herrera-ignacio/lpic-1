# Repositories

## Introduction

List of configured sources:

```
su
nano /etc/apt/sources.list
```

We want to have uncommented only the following:

```
deb http://deb.debian.org/debian stretch main contrib
```

This is because we are only using a testing environment and any other repositories are not needed.

Finally we can trigger a system update:

```
apt-get update
```
