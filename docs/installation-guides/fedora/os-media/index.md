---
icon: lucide/save
---

# Download Fedora Everything

## Installation

!!! warning

    If you do not already have a iso image on a [Ventoy](https://www.ventoy.net/en/index.html)
    stick, please refer to the [Ventoy installation guide](../../tools/os-tools/ventoy/index.md).

Navigate to fedoraproject.org:

* **[Get Fedora > Miscellaneous > Fedora Everything](https://fedoraproject.org/misc/#everything)**
* Download the version: **For Intel and AMD x86_64 systems**
* Move downloaded file to ventoy drive:

    ```shell
    cd ~/Downloads
    ls -lhrt
    cp Fedora-Everything-netinst-x86_64-*.iso /run/media/$USER/Ventoy/
    ```
