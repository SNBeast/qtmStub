# qtmStub

A patch to bypass qtm error 0xf9605002 for cases where servicing the camera hardware is not feasible.

## Effects

If qtm was broken without this patch applied, it will still be broken with this patch applied, and so Super-Stable 3D will not work. From my case, the 3D projection with this patch applied will behave as if Super-Stable 3D is turned off. Additionally, your cameras are unlikely to work and software which attempts to use them will likely crash or hang.

## Installation

Made for the latest version of qtm, v3072. That comes with a system firmware of at least 11.5.0-38, released July 10<sup>th</sup>, 2017.

If you believe your firmware may be too out of date, you can run System Update in Safe Mode by holding `L + R + Up + A` on boot. Safe Mode does not load qtm, so it can work without this patch.

### Luma3DS v13 or newer

1. Download `0004013000004202.ips` from the [latest release](https://github.com/SNBeast/qtmStub/releases/latest).
1. Copy `0004013000004202.ips` into `SD:/luma/sysmodules/`, creating folders as necessary. Ensure that its filename is preserved.
1. Enable the Luma setting `Enable loading external FIRMs and modules`, such as by using the menu accessed by holding Select on boot.

### Older Luma3DS versions

1. Download `0004013000004202.ips` from the [latest release](https://github.com/SNBeast/qtmStub/releases/latest).
1. Rename `0004013000004202.ips` to `code.ips`.
1. Copy `code.ips` into `SD:/luma/titles/0004013000004202/`, creating folders as necessary.
1. Enable *both* of the Luma settings `Enable loading external FIRMs and modules` and `Enable game patching`.
