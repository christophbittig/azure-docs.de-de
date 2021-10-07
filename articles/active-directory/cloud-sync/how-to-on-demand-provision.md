---
title: Bedarfsorientierte Bereitstellung in der Azure AD Connect-Cloudsynchronisierung
description: In diesem Artikel wird beschrieben, wie Sie mit der Cloudsynchronisierungsfunktion von Azure AD Connect Konfigurationsänderungen testen können.
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
ms.openlocfilehash: 20719182a9b97bc783ef2bdc16ccad327dba79aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742577"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Bedarfsorientierte Bereitstellung in der Azure AD Connect-Cloudsynchronisierung

Sie können mit der Cloudsynchronisierungsfunktion von Azure Active Directory Connect (Azure AD) Konfigurationsänderungen testen, indem Sie diese Änderungen auf einen einzelnen Benutzer anwenden. Mit dieser bedarfsorientierten Bereitstellung können Sie überprüfen, ob die an der Konfiguration vorgenommenen Änderungen richtig angewendet wurden und ordnungsgemäß mit Azure AD synchronisiert werden.  

> [!IMPORTANT] 
> Wenn Sie die bedarfsorientierte Bereitstellung verwenden, werden die Bereichsfilter nicht auf den von Ihnen ausgewählten Benutzer angewendet. Sie können die bedarfsorientierte Bereitstellung für Benutzer verwenden, die sich außerhalb der von Ihnen angegebenen Organisationseinheiten befinden.

Weitere Informationen und ein Beispiel finden Sie im folgenden Video.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mW]

## <a name="validate-a-user"></a>Validieren eines Benutzers
Führen Sie die folgenden Schritte aus, um die bedarfsorientierte Bereitstellung zu verwenden:

1.  Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
2.  Wählen Sie **Azure AD Connect** aus.
3.  Wählen Sie **Cloudsynchronisierung verwalten** aus.

    ![Screenshot: Link zum Verwalten der Cloudsynchronisierung](media/how-to-install/install-6.png)
4. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.
5. Wählen Sie unter **Validieren** die Schaltfläche **Benutzer bereitstellen** aus. 

   ![Screenshot: Schaltfläche zur Bereitstellung eines Benutzers](media/how-to-on-demand-provision/on-demand-2.png)

6. Geben Sie im Bildschirm **Bedarfsgesteuerte Bereitstellung** den Distinguished Name eines Benutzers ein, und wählen Sie die Schaltfläche **Bereitstellen** aus.  
 
   ![Screenshot: Benutzername und Schaltfläche „Bereitstellen“](media/how-to-on-demand-provision/on-demand-3.png)
7. Nachdem die Bereitstellung abgeschlossen ist, wird für den erfolgreichen Vorgang ein Bildschirm mit vier grünen Häkchen angezeigt. Fehler werden ggf. auf der linken Seite angezeigt.

   ![Screenshot: Erfolgreich Bereitstellung](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Abrufen von Details zur Bereitstellung
Nun können Sie anhand der Benutzerinformationen überprüfen, ob die Änderungen, die Sie an der Konfiguration vorgenommen haben, angewendet wurden. Im verbleibenden Teil dieses Artikels werden die einzelnen Abschnitte der Details zu einem erfolgreich synchronisierten Benutzer beschrieben.

### <a name="import-user"></a>Benutzer importieren
Der Abschnitt **Benutzer importieren** enthält Informationen zu dem Benutzer, der aus Active Directory importiert wurde. Hier sehen Sie den Benutzer vor der Bereitstellung in Azure AD. Wählen Sie den Link **Details anzeigen** aus, um diese Informationen anzuzeigen.

![Screenshot: Schaltfläche zum Anzeigen von Details zu einem importierten Benutzer](media/how-to-on-demand-provision/on-demand-5.png)

Mithilfe diesen Informationen können Sie die verschiedenen Attribute sehen, die importiert wurden (und deren Werte). Wenn Sie eine benutzerdefinierte Attributzuordnung erstellt haben, wird der Wert hier angezeigt.

![Screenshot: Benutzerdetails](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Gültigkeitsbereich des Benutzers ermitteln
Der Abschnitt **Gültigkeitsbereich des Benutzers ermitteln** enthält Informationen darüber, ob der Benutzer, der in Azure AD importiert wurde, im Gültigkeitsbereich liegt. Wählen Sie den Link **Details anzeigen** aus, um diese Informationen anzuzeigen.

![Screenshot: Schaltfläche zum Anzeigen von Details zu einem Benutzerbereich](media/how-to-on-demand-provision/on-demand-7.png)

Mithilfe dieser Informationen können Sie feststellen, ob sich der Benutzer im Gültigkeitsbereich befindet.

![Screenshot: Benutzerbereichsdetails](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Benutzer zwischen Quell- und Zielsystem abgleichen
Der Abschnitt **Benutzer zwischen Quell- und Zielsystem abgleichen** enthält Informationen darüber, ob der Benutzer bereits in Azure AD vorhanden ist, und ob statt der Bereitstellung eines neuen Benutzers eine Verknüpfung durchgeführt werden sollte. Wählen Sie den Link **Details anzeigen** aus, um diese Informationen anzuzeigen.

![Screenshot: Schaltfläche zum Anzeigen von Details zu einem übereinstimmenden Benutzer](media/how-to-on-demand-provision/on-demand-8.png)

Anhand dieser Informationen können Sie feststellen, ob eine Entsprechung gefunden wurde oder ob ein neuer Benutzer erstellt wird.

![Screenshot: Benutzerinformationen](media/how-to-on-demand-provision/on-demand-11.png)

In den entsprechenden Details wird eine Meldung mit einem der drei folgenden Vorgänge angezeigt:
- **Erstellen**: Ein Benutzer wird in Azure AD erstellt.
- **Aktualisieren**: Ein Benutzer wird basierend auf einer an der Konfiguration vorgenommenen Änderung aktualisiert.
- **Löschen**: Ein Benutzer wird aus Azure AD entfernt.

Die genaue Meldung hängt vom Typ des ausgeführten Vorgangs ab.

### <a name="perform-action"></a>Aktion ausführen
Der Abschnitt **Aktion ausführen** enthält Informationen zu dem Benutzer, der nach dem Anwenden der Konfiguration in Azure AD bereitgestellt oder exportiert wurde. Hier sehen Sie den Benutzer nach der Bereitstellung in Azure AD. Wählen Sie den Link **Details anzeigen** aus, um diese Informationen anzuzeigen.

![Screenshot: Schaltfläche zum Anzeigen von Details zu einer ausgeführten Aktion](media/how-to-on-demand-provision/on-demand-9.png)

Anhand dieser Informationen können Sie die Werte der Attribute ermitteln, nachdem die Konfiguration angewendet wurde. Entsprechen sie den importierten Werten, oder weichen sie ab? Wurde die Konfiguration erfolgreich angewendet?  

Mithilfe dieses Prozesses können Sie die Attributtransformation über die Cloud zum Azure AD-Mandanten nachverfolgen.

![Screenshot: Details zu nachverfolgten Attributen](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Nächste Schritte 

- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)
- [Installieren der Azure AD Connect-Cloudsynchronisierung](how-to-install.md)
 