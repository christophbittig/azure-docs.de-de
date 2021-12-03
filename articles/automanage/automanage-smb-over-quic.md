---
title: SMB über QUIC mit Azure Automanage bewährten Methoden für Computer
description: Übersicht über die Verwaltung von SMB über QUIC mit Azure Automanage bewährten Methoden für Computer
author: daniellee-microsoft
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/1/2021
ms.author: jol
ms.openlocfilehash: 2246b54b6831d0e88581c80aedc4e39388c6f377
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327384"
---
# <a name="smb-over-quic-with-automanage-machine-best-practices"></a>SMB über QUIC mit Azure Automanage bewährten Methoden für Computer

SMB über QUIC bietet ein SMB-VPN für Telearbeiter, Benutzer mobiler Geräte und Zweigstellen mit sicherer, zuverlässiger Konnektivität mit Edgedateiservern über nicht vertrauenswürdige Netzwerke wie das Internet. Weitere Informationen zu SMB über QUIC und zum Konfigurieren von SMB über QUIC finden Sie unter [SMB über QUIC](https://aka.ms/smboverquic).

Darüber hinaus ist SMB über QUIC mit Automanage bewährten Methoden für Computer integriert, um die Verwaltung von SMB über QUIC zu vereinfachen. QUIC verwendet Zertifikate, um die Verschlüsselung zu ermöglichen, und Organisationen haben häufig Probleme, komplexe Public Key-Infrastrukturen zu verwalten. Automanage für bewährte Methoden für Computer stellt sicher, dass Zertifikate nicht ohne Warnung ablaufen und dass SMB über QUIC aktiviert bleibt, um eine maximale Dienstkontinuität zu gewährleisten.

## <a name="how-to-get-started"></a>Erste Schritte

> [!NOTE]
> Informationen zu den Voraussetzungen für die Verwendung der Automanage bewährten Methoden für Computer finden Sie unter [Aktivieren auf virtuellen Computern im Azure-Portal](quick-create-virtual-machines-portal.md).

> [!NOTE]
> Während der Vorschauphase können Sie über [diesen Link](https://aka.ms/automanage-ws-portal-preview) im Azure-Portal beginnen.

## <a name="enable-automanage-best-practices-when-creating-a-new-vm"></a>Aktivieren der Automanage bewährten Methoden beim Erstellen eines neuen virtuellen Computers

Führen Sie die folgenden Schritte aus, um die Automanage bewährten Methoden für SMB über QUIC auf einem virtuellen Computer zu aktivieren:

1. Melden Sie sich beim Azure-Portal über den obigen Vorschaulink an.

2. Erstellen Sie einen Azure-Virtuellen Computer mit dem _Windows Server 2022 Datacenter: Azure Edition_ Bild, um die Automanage für Windows Server-Funktionen einschließlich SMB über QUIC zu erhalten.

3. Wählen Sie auf der Registerkarte **Verwaltung** für die Azure Automanage Umgebung entweder **Dev/Test** oder **Produktion** aus, um die Automanage bewährten Methoden für Computer zu aktivieren.

    :::image type="content" source="media\automanage-smb-over-quic\create-vm-automanage-setting.png" alt-text="Aktivieren Sie Automanage beim Erstellen eines virtuellen Computers.":::

4. Konfigurieren Sie alle zusätzlichen Einstellungen nach Bedarf, und erstellen Sie den virtuellen Computer.

## <a name="enable-automanage-best-practices-on-existing-vms"></a>Aktivieren der Automanage bewährten Methoden auf vorhandenen virtuellen Computern

Sie können die Automanage bewährten Methoden auch für einen virtuellen Computer aktivieren, den Sie zuvor erstellt haben. Beachten Sie, dass der virtuelle Computer mit dem _Windows Server 2022 Datacenter: Azure Edition_ Bild erstellt worden sein muss, um Automanage für Windows Server-Funktionen, einschließlich SMB über QUIC, zu erhalten.

1. Navigieren Sie zu dem virtuellen Computer, den Sie zuvor erstellt haben.
2. Wählen Sie das Automanage Menü aus, dann wählen Sie entweder die Umgebung  **Dev/Test** oder **Produktion** aus, und klicken Sie dann **auf Aktivieren**.

    :::image type="content" source="media\automanage-smb-over-quic\vm-enable-automanage.png" alt-text="Aktivieren von Automanage einen vorhandenen virtuellen Computer.":::

## <a name="viewing-automanage-best-practice-compliance"></a>Anzeigen der Konformität von Automanage bewährten Methoden

Es kann einige Stunden dauern, bis die bewährten Methoden für Computer konfiguriert sind und dann die Richtlinien für bewährte Methoden dem virtuellen Computer zugewiesen und bewertet werden. Sobald dies abgeschlossen ist, werden die SMB über QUIC-Richtlinien und deren Status wie unten dargestellt angezeigt. Diese Richtlinien werden kontinuierlich automatisch bewertet, um sicherzustellen, dass SMB über QUIC ordnungsgemäß konfiguriert ist und dass die verwendeten Zertifikate gültig und fehlerfrei sind.

:::image type="content" source="media\automanage-smb-over-quic\vm-automanage-configured.png" alt-text="Anzeigen von SMB über QUIC-Richtlinien für einen virtuellen Computer.":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu SMB über QUIC](https://aka.ms/smboverquic)
