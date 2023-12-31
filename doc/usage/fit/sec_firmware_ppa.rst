.. SPDX-License-Identifier: GPL-2.0+

SEC Firmware and multiple loadable images
=========================================

Example FIT image description file demonstrating the usage
of SEC Firmware and multiple loadable images loaded by U-Boot.
For booting PPA (SEC Firmware), "firmware" is searched and loaded.

Multiple binaries will be loaded as "loadables" (if present) at their
respective load offsets from firmware image address.

::

    /dts-v1/;

    /{
        description = "PPA Firmware";
        #address-cells = <1>;
        images {
            firmware@1 {
                description = "PPA Firmware: <version>";
                data = /incbin/("../obj/monitor.bin");
                type = "firmware";
                arch = "arm64";
                compression = "none";
            };
            trustedOS@1 {
                description = "Trusted OS";
                data = /incbin/("../../tee.bin");
                type = "OS";
                arch = "arm64";
                compression = "none";
                load = <0x00200000>;
            };
            fuse_scr {
                description = "Fuse Script";
                data = /incbin/("../../fuse_scr.bin");
                type = "firmware";
                arch = "arm64";
                compression = "none";
                load = <0x00180000>;
            };
        };

        configurations {
            default = "config-1";
            config-1 {
                description = "PPA Secure firmware";
                firmware = "firmware@1";
                loadables = "trustedOS@1", "fuse_scr";
            };
        };
    };
