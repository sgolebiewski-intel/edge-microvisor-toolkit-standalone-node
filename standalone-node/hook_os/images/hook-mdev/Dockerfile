# SPDX-FileCopyrightText: (C) 2025 Intel Corporation
# SPDX-License-Identifier: Apache-2.0

FROM alpine

USER root:root

RUN apk add --no-cache mdev-conf util-linux util-linux-misc busybox && rm -rf /var/cache/apk/*

# Overwrite the persistent storage script
COPY persistent-storage.sh lib/mdev/persistent-storage
RUN chmod +x lib/mdev/persistent-storage

USER nobody

CMD ["mdev", "-v", "-df"]

# 	-v	Verbose
#	-S	Log to syslog too
#	-s	Scan /sys and populate /dev
#	-d	Daemon, listen on netlink
#	-f	Run in foreground
