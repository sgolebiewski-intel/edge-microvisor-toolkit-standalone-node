# Edge Microvisor Toolkit Tink

*? What is the official name?*

* Edge Microvisor Toolkit Tink Minimal
* Edge Microvisor Toolkit Tink Micro (µOS) *? Needs clarification*

## Overview

*? Needs clarification*

### How it works

*? Needs clarification*

### Key features

*? Needs clarification*


## System Requirements

**Supported Hardware**

* Xeon, Core Ultra, Core and Atom processors

## Get Started

Download the OS image *? Needs a link*

or build, using the instructions below.


## Build the OS image

EMT-Tink image is built from EMT baseline same as other EMT images and generates a rootfs.tar.gz file.

A bash script generate-tink-initramfs.sh is provided in EMT baseline which could be used to generated the required initramfs and vmlinuz images to be used in place of HookOS images.
[Source](https://github.com/open-edge-platform/edge-microvisor-toolkit/blob/3.0/toolkit/imageconfigs/scripts/generate-tink-initramfs.sh)

Usage example:

```bash
sudo toolkit/imageconfigs/scripts/generate-tink-initramfs.sh \
  -f <emt-tink.tar.gz> -o <output_images_dir>
```

where
<emt-tink.tar.gz> is the rootfs tar.gz generated from EMT-Tink build
<output_images_dir> is the folder where output vmlinuz/initramfs files to be placed

generate-tink-initramfs.sh script should be called by EMT-Tink build CI to generate required vmlinuz and initramfs images to be picked up for HookOS replacement.

The generated images can then be picked up for EMF orchestrator or EMT-S build for injecting required customizations.

To boot with EMT-Tink vmlinuz and initramfs images, the following additional kernel parameters will be required:

```text
root=tmpfs rootflags=mode=0755 rd.skipfsck noresume modules-load=nbd
```

## EMF Specific Builds



A full description of EMF build flow reflecting the **emf_build_flow.png** diagram

See the diagram for more details:

![emf_build_flow](./emf_build_flow.png)


Add a comparison table/diagram
EMF Builds with HookOS vs. EMF Build with EMT-Tink

### EMF Build with HookOS (current workflow)

In current EMF with hookOS, the following are built directly into HookOS image to generate EMF customized HookOS initramfs and vmlinuz images:

- caddy docker image + EMF caddy config for hookOS [Ref](https://github.com/open-edge-platform/infra-onboarding/blob/69402c21b34eefa430f3d0eb2540f1949a1b8a33/hook-os/hook.yaml#L276https://github.com/open-edge-platform/infra-onboarding/blob/69402c21b34eefa430f3d0eb2540f1949a1b8a33/hook-os/hook.yaml#L275)
- device discovery agent docker image [ref](https://github.com/open-edge-platform/infra-onboarding/tree/main/hook-os/device_discovery)
- Fluent-bit docker image + EMF fluent-bit config for hookOS [Ref](https://github.com/open-edge-platform/infra-onboarding/tree/main/hook-os/fluent-bit)

Generated customized HookFS initramfs and vmlinuz images are then downloaded to edge node over PXE boot.
HookOS pulls tink worker container image after booting to start running Tinkerbell workflow. In HookOS case, tink worker is a container which runs other containers in a docker-in-docker scenario.

### EMF Build with EMT-Tink (**new workflow**)

With EMT-Tink, caddy, fluent-bit, device discovery agent and tink worker are run as native systemd services in EMT OS.

Caddy and fluent-bit are existing rpm packages which are included in EMT-Tink.
Device discovery agent from EMF infra-onboarding github is build as rpm package to run as systemd service and included in EMT-Tink image: [PR](https://github.com/open-edge-platform/edge-microvisor-toolkit/pull/118)

tink worker is built as rpm to run as systemd service and included in EMT-Tink image: [PR](https://github.com/open-edge-platform/edge-microvisor-toolkit/pull/106)
tink worker in EMT-Tink is also patched such that it directly runs containers via using containerd only, without dependency on docker and avoiding docker-in-docker use case.

EMT-Tink will provide above vmlinuz and initramfs images for release to be used in EMF and EMT-S installer builds.

EMF orchestrator build will need adjustments to inject the following into EMT-Tink initramfs during build to generate the final EMF customized initramfs file for EMT-Tink case:

- EMF caddy config files
- fluent-bit config files
- Environment configuration file
- Cert files

## EMT-S Specific Builds

*? Suggestion for content:*

A full description of EMT-S build workflow. Presented in a diagram below.

See the diagram for more details:

![emt_s_build_flow](../../../images/emts_s_build_flow.png)

Add a comparison table *? Can we add such a table to show the differences?*
EMT-S Builds with HookOS vs. EMT-S Build with EMT-Tink


### EMT-S build with hookOS

In EMT-Standalone, current separate HookOS sources from that used in EMF build is being used to generate required Hook OS images used in EMT-S installer.
EMT-S OS installer scripts are built into this EMT-S HookOS image and and setup to auto run in bash on boot. [Ref](https://github.com/intel-innersource/frameworks.edge.one-intel-edge.edge-node.standalone-edge-node/blob/main/hook_os/files/install-os.sh)

Generated customized HookOS initramfs and vmlinuz are then used to generate EMT-S required iso for usb installer.

### EMT-S build with EMT-Tink (**new workflow**)

For this case, the same approach as being done with Hook OS will be used.

EMT-S build will need adjustments to make the following changes into EMT-Tink initramfs before generate final iso for usb installer.

- include pkgs efibootmgr / gawk / lvm2 / net-tools / parted for EMT-S *? Needs clarification. What are the steps/respective commands?*
- inject required EMT-S OS installer bash scripts and systemd service to run it as service
- disable tink worker, caddy, fluent-bit, device discovery agent services

*? Needs clarification*





