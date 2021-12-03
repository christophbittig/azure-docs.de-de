---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: eur
ms.openlocfilehash: 34a45c4759954c0c0679dcafec13b5327a82cffd
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132252280"
---
:::row:::
    :::column span="3":::
        Das Speech SDK unterstützt nur **Ubuntu 16.04** (bis September 2021), **Ubuntu 18.04/20.04**, **Debian 9/10**, **Red Hat Enterprise Linux (RHEL) 7/8** und **CentOS 7/8** auf den folgenden Zielarchitekturen bei Verwendung mit Linux:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für die C++-Entwicklung
- x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für Java
- x64, ARM32 (Debian/Ubuntu) und ARM64 (Debian/Ubuntu) für .NET Core
- x64 für Python

> [!IMPORTANT]
> Für C# unter Linux ARM64 ist .NET Core 3.x (Paket „dotnet-sdk-3.x“) erforderlich.

> [!NOTE]
> Erstellen Sie eine Debian chroot-Umgebung gemäß der Dokumentation im Alpine Linux-Wiki unter [Ausführen von glibc-Programmen](https://wiki.alpinelinux.org/wiki/Running_glibc_programs), und führen Sie dann die hier aufgeführten Anweisungen für Debian aus, um das Speech SDK in Alpine Linux zu verwenden.

### <a name="system-requirements"></a>Systemanforderungen

Bei einer nativen Anwendung basiert das Speech SDK auf `libMicrosoft.CognitiveServices.Speech.core.so`. Stellen Sie sicher, dass die Zielarchitektur (x86, x64) mit der Anwendung übereinstimmt. Abhängig von der Linux-Version können zusätzliche Abhängigkeiten erforderlich sein.

- Freigegebene Bibliotheken der GNU C-Bibliothek (einschließlich der POSIX Threads Programming-Bibliothek `libpthreads`)
- OpenSSL-Bibliothek (`libssl`)
- Freigegebene Bibliothek für ALSA-Anwendungen (`libasound`)

# <a name="ubuntu-18042004"></a>[Ubuntu 18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl-dev libasound2 wget
```

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 und CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum groupinstall "Development tools"
sudo yum install alsa-lib openssl wget
```

> [!IMPORTANT]
> - Befolgen Sie in RHEL/CentOS 7 die Anweisungen zum [Konfigurieren von RHEL/CentOS 7 für das Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - Befolgen Sie unter RHEL/CentOS 8 die Anweisungen zum [Konfigurieren von OpenSSL für Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
