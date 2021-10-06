---
title: Erstellen einer neuen Konfiguration für den Azure AD Connect-Cloudsynchronisierungs-Agent
description: In diesem Artikel wird das Installieren der Cloudsynchronisierung beschrieben.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/10/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1a8602b18c4ec68eacc37faca941211ee83fd63
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742639"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Erstellen einer neuen Konfiguration für die Azure AD Connect-Cloudsynchronisierung

Das folgende Dokument führt Sie durch die Konfiguration der Azure AD Connect-Cloudsynchronisierung. Weitere Informationen und ein Beispiel für die Konfiguration der Cloudsynchronisierung finden Sie im folgenden Video.


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWKact]


## <a name="configure-provisioning"></a>Konfigurieren der Bereitstellung
Gehen Sie zum Konfigurieren der Bereitstellung wie folgt vor.

 1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 2. Wählen Sie **Azure AD Connect** aus.
 3. Wählen Sie **Cloudsynchronisierung verwalten** aus.

 ![Verwalten der Bereitstellung](media/how-to-install/install-6.png)
 
 4. Wählen Sie **Neue Konfiguration** aus.
 5. Wählen Sie auf dem Konfigurationsbildschirm Ihre Domäne aus, und geben Sie an, ob die Kennworthashsynchronisierung aktiviert werden soll.  Klicken Sie auf **Erstellen**.  
 
 ![Erstellen einer neuen Konfiguration](media/how-to-configure/configure-1.png)


 6.  Der Bildschirm „Edit provisioning configuration“ (Bereitstellungskonfiguration bearbeiten) wird geöffnet.

   ![Bearbeiten der Konfiguration](media/how-to-configure/con-1.png)

 7. Geben Sie eine **E-Mail-Adresse für Benachrichtigungen** ein. An diese E-Mail-Adresse wird eine Benachrichtigung gesendet, wenn die Bereitstellung nicht fehlerfrei ist.  Es wird empfohlen, das **Verhindern des versehentlichen Löschens** aktiviert zu lassen und den **Schwellenwert für versehentliches Löschen** auf eine Zahl festlegen, ab der Sie benachrichtigt werden möchten.  Weitere Informationen finden Sie unter [Versehentliches Löschen](#accidental-deletions) weiter unten.
 8. Verschieben Sie den Auswahlregler auf Aktivieren, und wählen Sie Speichern aus.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Beschränken der Bereitstellung auf bestimmte Benutzer und Gruppen
Durch Verwenden lokaler Active Directory-Gruppen oder Organisationseinheiten können Sie den Agent so einrichten, dass nur bestimmte Benutzer und Gruppen synchronisiert werden. Gruppen und Organisationseinheiten können im Rahmen einer Konfiguration nicht konfiguriert werden. 

 1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 2. Wählen Sie **Azure AD Connect** aus.
 3. Wählen Sie **Cloudsynchronisierung verwalten** aus.
 4. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.

 ![Konfigurationsabschnitt](media/how-to-configure/scope-1.png)
 
 5. Wählen Sie unter **Konfigurieren** die Option **Edit scoping filters** (Bereichsfilter bearbeiten) aus, um den Bereich der Konfigurationsregel zu ändern.
 ![Bearbeiten des Bereichs](media/how-to-configure/scope-3.png)
 7. Auf der rechten Seite können Sie den Bereich ändern.  Klicken Sie abschließend auf **Fertig** und **Speichern**.
 8. Nachdem Sie den Bereich geändert haben, sollten Sie die [Bereitstellung erneut starten](#restart-provisioning), um eine sofortige Synchronisierung der Änderungen einzuleiten.

## <a name="attribute-mapping"></a>Attributzuordnung
Die Azure AD Connect-Cloudsynchronisierung ermöglicht das einfache Zuordnen von Attributen zwischen lokalen Benutzer-/Gruppenobjekten und den Objekten in Azure AD.  Sie können die Standardattributzuordnungen den Anforderungen Ihres Unternehmens entsprechend anpassen. Dies bedeutet, dass Sie vorhandene Attributzuordnungen ändern oder löschen und neue Attributzuordnungen erstellen können.  Weitere Informationen finden Sie unter [Attributzuordnung bei der Azure AD Connect-Cloudbereitstellung](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Bedarfsorientierte Bereitstellung
Die Azure AD Connect-Cloudsynchronisierung ermöglicht Ihnen das Testen von Konfigurationsänderungen, indem diese Änderungen auf einen einzelnen Benutzer oder eine einzelne Gruppe angewandt werden.  Dadurch können Sie überprüfen, ob die an der Konfiguration vorgenommenen Änderungen richtig angewendet wurden und ordnungsgemäß mit Azure AD synchronisiert werden.  Weitere Informationen finden Sie unter [Bedarfsorientierte Bereitstellung mit Azure AD Connect-Cloudbereitstellung](how-to-on-demand-provision.md).

## <a name="accidental-deletions"></a>Versehentliches Löschen
Die Funktion gegen versehentliches Löschen dient zum Schutz vor unbeabsichtigten Konfigurationsänderungen und Änderungen an Ihrem lokalen Verzeichnis, die sich auf viele Benutzer und Gruppen auswirken würden.  Dieses Feature bietet folgende Möglichkeiten:

- Konfigurieren der Möglichkeit zum automatischen Verhindern von versehentlichen Löschungen 
- Festlegen der Anzahl von Objekten (Schwellenwert), ab der die Konfiguration wirksam wird 
- Einrichten einer E-Mail-Adresse für Benachrichtigungen, sobald der betreffende Synchronisierungsauftrag gemäß diesem Szenario in Quarantäne gestellt wird 

Weitere Informationen finden Sie unter [Versehentliches Löschen](how-to-accidental-deletes.md).

## <a name="quarantines"></a>Quarantäne
Die Cloudsynchronisierung überwacht die Integrität Ihrer Konfiguration und versetzt fehlerhafte Objekte in den Status „Quarantäne“. Wenn die meisten oder alle Aufrufe an das Zielsystem aufgrund eines Fehlers (z. B. bei ungültigen Administratoranmeldeinformationen) dauerhaft nicht erfolgreich sind, wird der Synchronisierungsauftrag in den Quarantänestatus versetzt.  Weitere Informationen finden Sie im Abschnitt zur Problembehandlung bei der [Quarantäne](how-to-troubleshoot.md#provisioning-quarantined-problems).

## <a name="restart-provisioning"></a>Erneutes Starten der Bereitstellung 
Wenn Sie nicht auf die nächste geplante Ausführung warten möchten, können Sie mithilfe der Schaltfläche **Bereitstellung neu starten** die Ausführung der Bereitstellung auslösen. 
 1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 2. Wählen Sie **Azure AD Connect** aus.
 3.  Wählen Sie **Cloudsynchronisierung verwalten** aus.
 4. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.

   ![Konfigurationsauswahl zum Neustart der Bereitstellung](media/how-to-configure/scope-1.png)

 5. Wählen Sie am oberen Rand **Bereitstellung neu starten** aus.

## <a name="remove-a-configuration"></a>Entfernen einer Konfiguration
Führen Sie die folgenden Schritte aus, um eine Konfiguration zu löschen.

 1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 2. Wählen Sie **Azure AD Connect** aus.
 3. Wählen Sie **Cloudsynchronisierung verwalten** aus.
 4. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.
   
   ![Konfigurationsauswahl zum Entfernen der Konfiguration](media/how-to-configure/scope-1.png)

 5. Wählen Sie am oberen Rand des Konfigurationsbildschirms **Löschen** aus.

>[!IMPORTANT]
>Vor dem Löschen einer Konfiguration wird keine Bestätigung ausgegeben. Stellen Sie sicher, dass es sich um die Aktion handelt, die Sie ausführen möchten, bevor Sie **Löschen** auswählen.


## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)
