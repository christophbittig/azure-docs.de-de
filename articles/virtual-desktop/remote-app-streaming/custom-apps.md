---
title: Hosten benutzerdefinierter Apps mit Azure Virtual Desktop – Azure
description: Hier erfahren Sie, wie Sie benutzerdefinierte Apps mit Azure Virtual Desktop bereitstellen.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 35e07fad22760e6c8c87e60f77cd6d98e5db42a2
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113798950"
---
# <a name="how-to-host-custom-apps-with-azure-virtual-desktop"></a>Hosten benutzerdefinierter Apps mit Azure Virtual Desktop

Azure Virtual Desktop kann mehrere Arten von Windows-Anwendungen hosten. Es wird empfohlen, Ihre Apps entsprechend der Art der App-Pakete vorzubereiten, mit denen Sie Ihre Apps bereitstellen möchten. In diesem wird erläutert Artikel, wie Sie für jede Art von App-Paket vorgehen müssen. 

>[!NOTE]
>Es wird empfohlen, Ihre Apps auf einem Host mit mehreren Sitzungen zu hosten. Außerdem wird empfohlen, dass Sie Ihre Apps testen, um sicherzustellen, dass sie sich wie erwartet verhalten, während sie auf Ihrem Host mit mehreren Sitzungen ausgeführt werden. Führen Sie z. B. einen Test aus, um zu prüfen, ob zwei oder mehr Benutzer auf demselben Sitzungshost die App erfolgreich gleichzeitig ausführen können.

## <a name="msix"></a>MSIX

MSIX ist der empfohlene Pakettyp für benutzerdefinierte Apps in Azure Virtual Desktop, da sie die Vorteile des integrierten [MSIX-Features zum Anfügen von Apps](../app-attach-glossary.md) des Dienstes nutzen können. Um zu erfahren, wie Sie vorhandene Win32-Anwendungen in das MSIX-Format neu paketieren können, besuchen Sie [Erneutes Paketieren Ihrer vorhandenen Win32-Anwendungen in das MSIX-Format](/windows/application-management/msix-app-packaging-tool).

Nachdem Sie Ihre App im MSIX-Format verpackt haben, können Sie das MSIX-Feature von Azure Virtual Desktop verwenden, um Ihre Apps an Ihre Kunden zuzustellen. Erfahren Sie, wie Sie das MSIX-Feature zum Anfügen von Apps für Ihre Apps verwenden können, unter [Bereitstellen von Apps mit dem MSIX-Feature zum Anfügen von Apps](msix-app-attach.md).

## <a name="other-options-for-win32-applications"></a>Weitere Optionen für Win32-Anwendungen

Sie können Ihren Benutzern auch Win32-Anwendungen anbieten, ohne sie im MSIX-Format neu zu paketieren, indem Sie die folgenden Optionen verwenden.

### <a name="include-the-application-manually-on-session-hosts"></a>Manuelles Einbeziehen der Anwendung auf Sitzungshosts

Folgen Sie den Anweisungen unter [Vorbereiten und Anpassen eines VHD-Masterimages](../set-up-customize-master-image.md), um eine App als Teil des Windows-Images einzuschließen, das Sie für Ihre virtuellen Computer verwenden. Befolgen Sie insbesondere die Anweisungen im Abschnitt [Konfiguration anderer Anwendungen und der Registrierung](../set-up-customize-master-image.md#other-applications-and-registry-configuration), um die Anwendung für alle Benutzer zu installieren.

### <a name="use-microsoft-endpoint-manager-to-deploy-the-application-at-scale"></a>Verwenden von Microsoft Endpoint Manager zum Bereitstellen der Anwendung im großen Stil

Wenn Sie Microsoft Endpoint Manager zur Verwaltung Ihrer Sitzungshosts verwenden, können Sie Anwendungen mithilfe der Anweisungen in [Installieren von Apps auf Windows 10-Geräten](/mem/intune/apps/apps-windows-10-app-deploy#install-apps-on-windows-10-devices) bereitstellen. Stellen Sie sicher, dass Sie Ihre App im Modus „Gerätekontext“ auf allen Sitzungshosts bereitstellen, um sicherzustellen, dass alle Benutzer in Ihrer Bereitstellung auf die Anwendung zugreifen können.

### <a name="manual-installation"></a>Manuelle Installation

Es wird nicht empfohlen, Apps manuell zu installieren, da der Vorgang für jeden Sitzungshost wiederholt werden muss. Diese Methode wird häufiger von IT-Experten zu Testzwecken verwendet.

Wenn Sie Ihre Apps manuell installieren müssen, müssen Sie mit einem Administratorkonto eine Remoteverbindung mit dem Sitzungshost herstellen, nachdem Sie Ihren Azure Virtual Desktop-Hostpool eingerichtet haben. Danach installieren Sie die Anwendung wie auf einem physischen PC. Sie müssen diesen Vorgang wiederholen, um die Anwendung auf jedem Sitzungshost in Ihrem Hostpool zu installieren.

>[!NOTE]
>Wenn Sie während des Einrichtungsvorgangs die Möglichkeit erhalten, die Anwendung für alle Benutzer zu installieren, wählen Sie diese Option aus.

## <a name="microsoft-store-applications"></a>Microsoft Store-Anwendungen

Es wird derzeit nicht empfohlen, Apps aus dem Microsoft Store für das Streaming von Remote-Apps in Azure Virtual Desktop zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie Apps mithilfe des MSIX-Features zum Anfügen von Apps verpacken und bereitstellen können, erfahren Sie unter [Bereitstellen von Apps mit dem MSIX-Feature zum Anfügen von Apps](msix-app-attach.md).