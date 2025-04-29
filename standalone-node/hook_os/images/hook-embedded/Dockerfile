# SPDX-FileCopyrightText: (C) 2025 Intel Corporation
# SPDX-License-Identifier: Apache-2.0
FROM alpine:3.19 as builder
RUN adduser -D -u 1000 embeddeduser

FROM scratch
ENTRYPOINT []
WORKDIR /
COPY ./images/ /etc/embedded-images/
# the name 001 is important as that is the order in which the scripts are executed
# we need this mounting to happen before the other init.d scripts run so that
# the mount points are available to them.
COPY ./images-mount.sh /etc/init.d/001-images-mount.sh
COPY --from=builder /etc/passwd /etc/passwd

# Set non-root user
USER embeddeduser

CMD []
