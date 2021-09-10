---
title: Neuigkeiten für Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Versionshinweise für Azure Data Science Virtual Machine
author: michalmar
ms.service: data-science-vm
ms.author: mimarusa
ms.date: 07/16/2021
ms.topic: reference
ms.openlocfilehash: b74b535a6d6e00f83dd659728bb1ed930e1fb7dd
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371376"
---
# <a name="azure-data-science-virtual-machine-release-notes"></a>Azure Data Science Virtual Machine – Versionshinweise

Dieser Artikel enthält Informationen zu Azure Data Science Virtual Machine-Releases. Eine vollständige Liste der enthaltenen Tools sowie Versionsnummern finden Sie auf [dieser Seite](./tools-included.md).

In der [Liste mit bekannten Problemen](reference-known-issues.md) finden Sie Informationen zu bekannten Fehlern und Problemumgehungen.

## <a name="2021-07-12"></a>2021-07-12

Neues Image für [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)

Hauptänderungen:

- Update auf PyTorch 1.9.0
- Update der Azure CLI auf 2.26.1
- Update der Azure Machine Learning-Erweiterung der Azure CLI auf 1.29.0
- Update der VS Code-Version auf 1.58.1
- Verbesserung der Stabilität und kleinere Fehlerbehebungen 


## <a name="2021-06-22"></a>2021-06-22

Neues Image für [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)

Version: 22.06.21

Hauptänderungen:

- Update auf PyTorch 1.9.0
- Ein Fehler wurde behoben, bei dem Git nicht verfügbar war.


## <a name="2021-06-01"></a>2021-06-01

Neues Image für [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)

Version: 21.06.01

Hauptänderungen:

- Docker ist standardmäßig aktiviert.
- JupyterHub verwendet JupyterLab standardmäßig.
- Aktualisierte Python-Versionen zur Behebung von [CVE-2020-15523](https://nvd.nist.gov/vuln/detail/CVE-2020-15523)
- Aktualisierte IntelliJ-IDEA-Version auf Version 2021.1 zur Behebung von [CVE-2021-25758](https://nvd.nist.gov/vuln/detail/CVE-2021-25758)
- Aktualisierung von PyCharm Community auf 2021.1
- Aktualisierung von TensorFlow auf Version 2.5.0

<br/>
Mehrere Symbole wurden vom Desktop entfernt.

## <a name="2021-05-22"></a>2021-05-22

Neues Image für [Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)

Version: 21.05.22

Ausgewählte Versionsupdates:
- CUDA 11.1
- Python 3.8
- PyTorch 1.8.1
- TensorFlow 2.5.0
- Spark 3.1.1
- Java 11
- R 4.1.0
- Julia 1.0.5
- NodeJS 16.2.0
- Visual Studio Code 1.56.2 einschließlich Azure ML-Erweiterung
- PyCharm Community Edition 2021.1.1
- Jupyter Lab 2.2.6
- RStudio 1.4.1106
- Visual Studio Community-Version 2019 (Version 16.9.6)
- Azure CLI 2.23.0
- Storage-Explorer 1.19.1
- AzCopy 10.10.0
- Power BI Desktop 2.93.641.0 64 Bit (Mai 2021)
- Azure Data Studio 1.28.0
- Microsoft Edge

<br/>
Firefox, Apache Drill und Microsoft Integration Runtime wurden entfernt.

<br/>
Dunkler Modus, geänderte Symbole auf dem Desktop, Änderung an den Hintergrundbildern.

## <a name="2021-05-12"></a>2021-05-12

Neues Image für [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)

Ausgewählte Versionsupdates:
- CUDA 11.3, cuDNN 8, NCCL2
- Python 3.8
- R 4.0.5
- Spark 3.1 einschließlich mmlspark, Connectors für Blob Storage, Data Lake, CosmosDB
- Java 11 (OpenJDK)
- Jupyter Lab 3.0.14
- PyTorch 1.8.1 einschließlich torchaudio torchtext torchvision, torch-tb-profiler
- TensorFlow 2.4.1 einschließlich TensorBoard
- dask 2021.01.0
- VS.Code 1.56
- Azure Data Studio 1.22.1
- Azure CLI 2.23.0
- Azure Storage Explorer 1.19.1
- azcopy 10.10
- Microsoft Edge-Browser (Beta)

<br/>
Docker wurde hinzugefügt. Der Docker-Dienst wird standardmäßig nicht gestartet, um Ressourcen zu sparen. Führen Sie die folgenden Befehlszeilenbefehle aus, um den Docker-Dienst zu starten:

```
sudo systemctl start docker
```

> [!NOTE]
> Wenn Ihr Computer über GPU(s) verfügt, können Sie die GPU(s) innerhalb der Container nutzen, indem Sie Ihrem Docker-Befehl einen Parameter `--gpus` hinzufügen.
>
> Die Ausführung von  gibt beispielsweise
>
> `sudo docker run --gpus all -it --rm -v local_dir:container_dir nvcr.io/nvidia/pytorch:18.04-py3`
>
> Dadurch wird ein Ubuntu 18.04-Container mit PyTorch (vorinstalliert) und allen GPUs aktiviert ausgeführt. Außerdem wird ein lokaler Ordner *local_dir* im Container unter *container_dir* verfügbar gemacht.
>


## <a name="2020-02-24"></a>2020-02-24

Data Science Virtual Machine-Images für [Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)- und [Windows 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019?tab=Overview)-Images sind jetzt verfügbar.

## <a name="2019-10-08"></a>2019-10-08

### <a name="updates-to-software-on-the-windows-dsvm"></a>Softwareupdates für die Windows-DSVM-Instanz

- Azure Storage Explorer 1.10.1
- Power BI Desktop 2.73.55xx
- Firefox 69.0.2
- PyCharm 19.2.3
- RStudio 1.2.50xx

### <a name="default-browser-for-windows-updated"></a>Standardbrowser für Windows aktualisiert

Zuvor war der Standardbrowser auf Internet Explorer festgelegt. Benutzer werden nun bei der ersten Anmeldung aufgefordert, einen Standardbrowser auszuwählen.
