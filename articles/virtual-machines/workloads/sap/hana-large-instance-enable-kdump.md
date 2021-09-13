---
title: Skript zum Aktivieren von kdump in SAP HANA (große Instanzen) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie den kdump-Dienst in Azure HANA (große Instanzen) vom Typ I und Typ II aktivieren.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 272ac309629c62ee9fc7d12236aac4b2c3106b8a
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112540922"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances"></a>kdump für SAP HANA in Azure (große Instanzen)

In diesem Artikel erfahren Sie den kdump-Dienst in Azure HANA (große Instanzen) (HLI) vom **Typ I und Typ II** aktivieren.

Das Konfigurieren und Aktivieren von Kdump ist erforderlich, um Probleme mit Systemabstürzen zu beheben, die keine klare Ursache haben. Manchmal kann ein Systemabsturz nicht durch Hardware- oder Infrastrukturprobleme erklärt werden. In solchen Fällen hat möglicherweise ein Betriebssystem oder eine Anwendung das Problem verursacht. Mit kdump kann SUSE den Grund für den Systemabsturz ermitteln.

## <a name="supported-skus"></a>Unterstützte SKUs

|  Typ der großen HANA-Instanz   |  Betriebssystemhersteller   |  Betriebssystem-Paketversion   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Voraussetzungen

- Der kdump-Dienst verwendet das Verzeichnis `/var/crash` zum Schreiben von Absturzabbildern. Stellen Sie sicher, dass die Partition, die diesem Verzeichnis entspricht, über ausreichend Speicherplatz verfügt, um Absturzabbilder zu speichern.

## <a name="setup-details"></a>Details zur Konfiguration

- Das Skript zum Aktivieren von kdump finden Sie unter [Azure sap-hana-tools auf GitHub](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh).

> [!NOTE]
> Dieses Skript basiert auf unserem Lab-Setup. Sie müssen sich an Ihren Betriebssystemanbieter wenden, um weitere Optimierungen zu erhalten.
> Eine separate logische Einheitennummer (LUN) wird für neue und vorhandene Server zum Speichern der Absturzabbilder bereitgestellt. Ein Skript übernimmt die Konfiguration des Dateisystems aus der LUN.
> Microsoft ist nicht für die Analyse des Abbilds verantwortlich. Sie müssen ein Ticket bei Ihrem Betriebssystemanbieter eröffnen, um es analysieren zu lassen.

- Führen Sie dieses Skript mithilfe des folgenden Befehls in Ihrer großen HANA-Instanz aus.

    > [!NOTE]
    > Zur Ausführung dieses Befehls ist die sudo-Berechtigung erforderlich.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Wenn die Ausgabe des Befehls anzeigt, dass kdump erfolgreich aktiviert ist, starten Sie das System neu, um die Änderungen zu übernehmen.

- Wenn in der Ausgabe des Befehls ein Vorgang fehlgeschlagen ist, ist der kdump-Dienst nicht aktiviert. In diesem Fall finden Sie weitere Informationen im folgenden Abschnitt [Probleme mit der Unterstützung](#support-issues).

## <a name="test-kdump"></a>Testen von kdump

> [!NOTE]
>  Mit dem folgenden Vorgang werden ein Kernel-Absturz und ein Systemneustart ausgelöst.

- Lösen Sie einen Kernel-Absturz aus.

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Überprüfen Sie nach erfolgreichem Systemneustart, ob das `/var/crash`-Verzeichnis Kernel-Absturzprotokolle enthält.

- Wenn `/var/crash` ein Verzeichnis mit dem aktuellen Datum enthält, wurde kdump erfolgreich aktiviert.

## <a name="support-issues"></a>Probleme mit der Unterstützung

Wenn das Skript mit einem Fehler fehlschlägt oder kdump nicht aktiviert wurde, wenden Sie sich mit einem Service Request das Microsoft-Supportteam. Geben Sie die folgenden Details an:

* HLI-Abonnement-ID

* Servername

* Betriebssystemhersteller

* Betriebssystemversion

* Kernelversion

Weitere Informationen finden Sie unter [Konfigurieren von kdump](https://www.suse.com/support/kb/doc/?id=3374462).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Betriebssystemupgrades auf HANA (große Instanzen).

> [!div class="nextstepaction"]
> [Betriebssystemupgrades](os-upgrade-hana-large-instance.md)
