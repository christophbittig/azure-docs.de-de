---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/20/2021
ms.author: glenga
ms.openlocfilehash: 162cfe73e57c314ed1b8200d5ecacebc92cfc517
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128669707"
---
Die folgenden Schritte verwenden [APT](https://wiki.debian.org/Apt) zum Installieren der Core Tools unter Ihrer Ubuntu/Debian Linux-Distribution. Informationen zu anderen Linux-Distributionen finden Sie in der [Infodatei zu den Core Tools](https://github.com/Azure/azure-functions-core-tools/blob/master/README.md#linux).

1. Installieren Sie den GPG-Schlüssel des Microsoft-Paketrepositorys, um die Paketintegrität zu überprüfen:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
    ```

1. Richten Sie die Liste der APT-Quellen ein, bevor Sie ein APT-Update ausführen.

    ##### <a name="ubuntu"></a>Ubuntu

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-$(lsb_release -cs)-prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

    ##### <a name="debian"></a>Debian

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/debian/$(lsb_release -rs | cut -d'.' -f 1)/prod $(lsb_release -cs) main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

1. Überprüfen Sie die Datei `/etc/apt/sources.list.d/dotnetdev.list` auf eine der entsprechenden Linux-Versionszeichenfolgen, die unten aufgeführt sind:

    | Linux-Verteilung | Version |
    | --------------- | ----------- |
    | Debian 10 | `buster`  |
    | Debian 9  | `stretch` |
    | Ubuntu 20.04    | `focal`     |
    | Ubuntu 19.04    | `disco`     |
    | Ubuntu 18.10    | `cosmic`    |
    | Ubuntu 18.04    | `bionic`    |
    | Ubuntu 17.04    | `zesty`     |
    | Ubuntu 16.04/Linux Mint 18    | `xenial`  |

1. Starten Sie das Update der APT-Quelle:

    ```bash
    sudo apt-get update
    ```