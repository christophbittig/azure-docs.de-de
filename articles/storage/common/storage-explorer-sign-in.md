---
title: Anmelden bei Azure Storage-Explorer | Microsoft-Dokumentation
description: Dokumentation zur Anmeldung bei Azure Storage-Explorer
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: bf7fe9dc4d8c60b7e8779100bd959ee7e85415e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583854"
---
# <a name="sign-in-to-storage-explorer"></a>Anmelden bei Storage-Explorer

Die Anmeldung ist die empfohlene Methode für den Zugriff auf Ihre Azure-Speicherressourcen mit Storage-Explorer. Durch die Anmeldung profitieren Sie von Berechtigungen auf Basis von Azure AD, etwa RBAC und Gen2 POSIX-ACLs.

## <a name="how-to-sign-in"></a>Anmeldung

Öffnen Sie zum Anmelden bei Storage-Explorer das Dialogfeld **Verbinden**. Sie können das Dialogfeld **Verbinden** entweder über die vertikale Symbolleiste auf der linken Seite oder durch Klicken auf **Konto hinzufügen...** im **Kontobereich** öffnen.

Nachdem Sie das Dialogfeld geöffnet haben, wählen Sie **Abonnement** als Ressourcentyp aus, mit dem Sie eine Verbindung herstellen möchten, und klicken Sie auf **Weiter**.

Sie müssen nun auswählen, in welcher Azure-Umgebung Sie sich anmelden möchten. Sie können eine bekannte Umgebung auswählen, etwa Azure oder Azure China, oder eine eigene Umgebung hinzufügen. Klicken Sie nach dem Auswählen Ihrer Umgebung auf **Weiter**.

Jetzt wird der **Standardwebbrowser** Ihres Betriebssystems gestartet, und eine Anmeldeseite wird geöffnet. Um optimale Ergebnisse zu erzielen, lassen Sie dieses Browserfenster während der gesamten Verwendung von Storage-Explorer oder zumindest so lange geöffnet, bis Sie alle erwarteten MFA-Schritte ausgeführt haben. Wenn die Anmeldung abgeschlossen ist, können Sie zu Storage-Explorer zurückkehren.

## <a name="managing-accounts"></a>Verwalten von Konten

Sie können Azure-Konten, bei denen Sie sich angemeldet haben, über den **Kontobereich** verwalten und entfernen. Sie können den **Kontobereich** öffnen, indem Sie auf der vertikalen Symbolleiste auf der linken Seite auf die Schaltfläche **Konten verwalten** klicken.

Im **Kontobereich** werden alle Konten angezeigt, bei denen Sie sich angemeldet haben. Unter jedem Konto wird Folgendes angezeigt:
- Die Mandanten, zu denen das Konto gehört
- Für jeden Mandanten die Abonnements, auf die Sie Zugriff haben

Storage-Explorer meldet Sie standardmäßig nur bei Ihrem Basismandanten an. Wenn Sie Abonnements und Ressourcen aus einem anderen Mandanten anzeigen möchten, müssen Sie diesen Mandanten aktivieren. Wenn Sie einen Mandanten aktivieren möchten, aktivieren Sie das Kontrollkästchen neben dem entsprechenden Mandanten. Wenn Sie einen Mandanten nicht mehr verwenden möchten, können Sie zum Deaktivieren des Mandanten das Kontrollkästchen deaktivieren. Sie können den Basismandanten nicht deaktivieren.

Nach dem Aktivieren eines Mandanten müssen Sie Ihre Anmeldeinformationen möglicherweise erneut eingeben, damit Storage-Explorer Abonnements laden oder auf Ressourcen aus dem Mandanten zugreifen kann. Die erneute Eingabe Ihrer Anmeldeinformationen ist in der Regel aufgrund einer Richtlinie für bedingten Zugriff (Conditional Access, CA) erforderlich, etwa für mehrstufige Authentifizierung (Multi-Factor Authentication, MFA). Auch wenn Sie die MFA möglicherweise bereits für einen anderen Mandanten ausgeführt haben, müssen Sie diesen Schritt unter Umständen erneut ausführen. Um Ihre Anmeldeinformationen erneut einzugeben, klicken Sie einfach auf **Anmeldeinformationen noch mal eingeben...** . Sie können auch auf **Fehlerdetails...** klicken, um genau zu ermitteln, warum Abonnements nicht geladen werden konnten.

Nachdem Ihre Abonnements geladen wurden, können Sie auswählen, welche Abonnements Sie filtern möchten, indem Sie die entsprechenden Kontrollkästchen aktivieren oder deaktivieren.

Wenn Sie Ihr gesamtes Azure-Konto entfernen möchten, klicken Sie neben dem Konto auf **Entfernen**.

## <a name="changing-where-sign-in-happens"></a>Ändern des Orts, an dem die Anmeldung erfolgt

Die Anmeldung erfolgt standardmäßig im **Standardwebbrowser** Ihres Betriebssystems. Die Anmeldung mit Ihrem Standardwebbrowser optimiert den Zugriff auf Ressourcen, die über Richtlinien für bedingten Zugriff (etwa MFA) geschützt sind. Wenn die Anmeldung über den **Standardwebbrowser** Ihres Betriebssystems aus irgendeinem Grund nicht funktioniert, können Sie ändern, wo oder wie Storage-Explorer die Anmeldung durchführt.

Suchen Sie in den **Einstellungen (Zahnradsymbol auf der linken Seite) unter**  > **Anwendung** > **Anmelden** nach der Einstellung **Anmelden mit**. Drei Optionen stehen zur Verfügung:
- **Standardwebbrowser:** Die Anmeldung erfolgt im **Standardwebbrowser** Ihres Betriebssystems. Diese Option wird empfohlen.
- **Integrierte Anmeldung:** Die Anmeldung erfolgt in einem Storage-Explorer-Fenster. Diese Option kann nützlich sein, wenn Probleme bei der Anmeldung mithilfe Ihres **Standardwebbrowsers** auftreten.
- **Gerätecodeflow:** Sie erhalten einen Code von Storage-Explorer, den Sie in ein Browserfenster eingeben können. Von der Verwendung dieser Option wird abgeraten. Der Gerätecodeflow ist mit vielen CA-Richtlinien nicht kompatibel.

## <a name="troubleshooting-sign-in-issues"></a>Behandeln von Problemen bei der Anmeldung

Wenn Sie Probleme bei der Anmeldung haben oder nach der Anmeldung Probleme mit einem Azure-Konto auftreten, lesen Sie den [Abschnitt „Probleme bei der Anmeldung“ im Azure Storage-Explorer-Leitfaden zur Problembehandlung](./storage-explorer-troubleshooting.md#sign-in-issues).

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten von Azure Blob Storage-Ressourcen mit Storage-Explorer](../../vs-azure-tools-storage-explorer-blobs.md)
- [Problembehandlung bei Anmeldeproblemen](./storage-explorer-troubleshooting.md#sign-in-issues)
