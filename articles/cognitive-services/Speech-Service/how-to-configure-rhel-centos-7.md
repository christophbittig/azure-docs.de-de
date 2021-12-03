---
title: Konfigurieren von RHEL/CentOS 7 – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie RHEL/CentOS 7 so konfigurieren, dass das Speech SDK verwendet werden kann.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: 8c7b9a6f8bb74bd8c66eac976bf9d17a3fd798d5
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132155973"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurieren von RHEL/CentOS 7 für das Speech SDK

Um das Speech SDK für die C++-Entwicklung unter Red Hat Enterprise Linux (RHEL) 8 x64 und CentOS 8 x64 zu verwenden, aktualisieren Sie den C++-Compiler und die gemeinsame C++-Laufzeitbibliothek auf Ihrem System.

### <a name="1-general-setup"></a>1. Allgemeine Einrichtung

Installieren Sie zunächst alle allgemeinen Abhängigkeiten:

```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. C/C++-Compiler und -Laufzeitbibliotheken

Installieren Sie mit dem folgenden Befehl die Pakete mit den erforderlichen Komponenten:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

Aktualisieren Sie als Nächstes den Compiler und die Laufzeitbibliotheken:

```bash
# Build GCC 7.5.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-7.5.0/gcc-7.5.0.tar.bz2 -O
tar jxf gcc-7.5.0.tar.bz2
mkdir gcc-7.5.0-build && cd gcc-7.5.0-build
../gcc-7.5.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Müssen der aktualisierte Compiler und die aktualisierten Laufzeitbibliotheken auf mehreren Computern bereitgestellt werden, können Sie sie einfach unter `/usr/local` kopieren und auf andere Computer übertragen. Wenn nur die Laufzeitbibliotheken erforderlich sind, reichen die Dateien unter `/usr/local/lib64` aus.

### <a name="3-environment-settings"></a>3. Umgebungseinstellungen

Führen Sie die folgenden Befehle aus, um die Konfiguration abzuschließen:

```bash
# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-<version>/lib/x64:$LD_LIBRARY_PATH

```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Informationen zum Speech SDK](speech-sdk.md)
