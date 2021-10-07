---
title: 'Azure AD Connect-Cloudsynchronisierung: Versehentliche Löschvorgänge'
description: In diesem Thema wird beschrieben, wie Sie die Funktion gegen versehentliches Löschen verwenden, um unerwünschte Löschvorgänge zu verhindern.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5055b847eab1ee53688abfa308b5969f7526f668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742680"
---
# <a name="accidental-delete-prevention"></a>Verhindern von versehentlichen Löschvorgängen

Dieses Dokument enthält eine Beschreibung der Funktion gegen versehentliches Löschen für die Azure AD Connect-Cloudsynchronisierung.  Die Funktion gegen versehentliches Löschen dient zum Schutz vor unbeabsichtigten Konfigurationsänderungen und Änderungen an Ihrem lokalen Verzeichnis, die sich auf viele Benutzer und Gruppen auswirken würden.  Dieses Feature bietet folgende Möglichkeiten:

- Konfigurieren der Möglichkeit zum automatischen Verhindern von versehentlichen Löschungen 
- Festlegen der Anzahl von Objekten (Schwellenwert), ab der die Konfiguration wirksam wird 
- Einrichten eine E-Mail-Adresse für Benachrichtigungen, sobald der betreffende Synchronisierungsauftrag gemäß diesem Szenario in Quarantäne gestellt wird 

Zum Verwenden dieser Funktion legen Sie den Schwellenwert für die Anzahl von gelöschten Objekten fest, bei dessen Erreichung die Synchronisierung angehalten wird.  Nachdem diese Anzahl erreicht wurde, wird die Synchronisierung angehalten und eine Benachrichtigung an die angegebene E-Mail-Adresse gesendet.  Anhand dieser Benachrichtigung können Sie untersuchen, was passiert ist.

Weitere Informationen und ein Beispiel finden Sie im folgenden Video.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mV]


## <a name="configure-accidental-delete-prevention"></a>Konfigurieren der Verhinderung von versehentlichen Löschvorgängen
Führen Sie die folgenden Schritte aus, um die neue Funktion zu verwenden.


1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
2.  Wählen Sie **Azure AD Connect** aus.
3.  Wählen Sie **Cloudsynchronisierung verwalten** aus.
4. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.
5. Geben Sie unter **Einstellungen** Folgendes an:
    - **Benachrichtigungs-E-Mail**: Enthält die für Benachrichtigungen verwendete E-Mail-Adresse.
    - **Versehentliches Löschen verhindern**: Aktivieren Sie dieses Kontrollkästchen, um die Funktion zu aktivieren.
    - **Schwellenwert für versehentliches Löschen**: Geben Sie die Anzahl von Objekten an, die erreicht werden muss, damit die Synchronisierung angehalten und eine Benachrichtigung gesendet wird.

![Versehentliche Löschvorgänge](media/how-to-accidental-deletes/accident-1.png)

## <a name="recovering-from-an-accidental-delete-instance"></a>Wiederherstellen nach einem versehentlichen Löschvorgang
Wenn bei Ihnen ein versehentlicher Löschvorgang auftritt, können Sie dies am Konfigurationsstatus Ihres Bereitstellungs-Agents erkennen.  Der Status lautet **Schwellenwert für Löschvorgänge überschritten**.
 
![Versehentlicher Löschvorgang: Status](media/how-to-accidental-deletes/delete-1.png)

Wenn Sie auf **Schwellenwert für Löschvorgänge überschritten** klicken, werden die Informationen zum Synchronisierungsstatus angezeigt.  Diese Informationen enthalten zusätzliche Details. 
 
 ![Synchronisierungsstatus](media/how-to-accidental-deletes/delete-2.png)

Wenn Sie mit der rechten Maustaste auf die Auslassungspunkte klicken, werden die folgenden Optionen angezeigt:
 - Bereitstellungsprotokolle anzeigen
 - Agent anzeigen
 - Löschvorgänge zulassen

 ![Rechtsklick](media/how-to-accidental-deletes/delete-3.png)

Mit der Option **Bereitstellungsprotokolle anzeigen** können Sie die Einträge unter **StagedDelete** anzeigen und die Informationen zu den Benutzern durchgehen, die gelöscht wurden.
 
 ![Bereitstellungsprotokolle](media/how-to-accidental-deletes/delete-7.png)

### <a name="allowing-deletes"></a>Löschvorgänge werden zugelassen

Mit der Aktion **Löschvorgänge zulassen** werden die Objekte gelöscht, die dazu geführt haben, dass der Schwellenwert für versehentliches Löschen erreicht wurde.  Verwenden Sie das folgende Verfahren, um die Löschvorgänge zu akzeptieren.  

1. Klicken Sie mit der rechten Maustaste auf die Auslassungspunkte, und wählen Sie **Löschvorgänge zulassen** aus.
2. Klicken Sie in der Bestätigungsmeldung auf **Ja**, um die Löschvorgänge zuzulassen.
 
 ![Option „Ja“ in Bestätigungsmeldung](media/how-to-accidental-deletes/delete-4.png)

3. Es wird eine Bestätigung angezeigt, dass die Löschvorgänge akzeptiert wurden. Für den nächsten Zyklus wird wieder „Fehlerfrei“ als Status angezeigt. 
 
 ![Löschvorgänge akzeptieren](media/how-to-accidental-deletes/delete-8.png)

### <a name="rejecting-deletions"></a>Ablehnen von Löschvorgängen

Gehen Sie wie folgt vor, falls Sie die Löschvorgänge nicht zulassen möchten:
- Informieren Sie sich über die Quelle der Löschvorgänge.
- Beheben Sie das Problem (Beispiel: OE wurde versehentlich aus dem Bereich verschoben, und Sie fügen die OE wieder dem Bereich hinzu).
- Führen Sie **Synchronisierung neu starten** für die Agentkonfiguration aus.

## <a name="next-steps"></a>Nächste Schritte 

- [Problembehandlung für die Cloudsynchronisierung](how-to-troubleshoot.md)
- [Azure AD Connect-Cloudsynchronisierung: Fehlercodes und Beschreibungen](reference-error-codes.md)
 

