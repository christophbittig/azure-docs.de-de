---
title: Quarantänestatus bei der Azure Active Directory-Anwendungsbereitstellung
description: Wenn Sie eine Anwendung für die automatische Benutzerbereitstellung konfiguriert haben, lesen Sie diesen Artikel, um zu erfahren, was der Bereitstellungsstatus „Quarantäne“ bedeutet und wie Sie ihn löschen können.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/11/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 93b00212bda4f02b6a31c151856639b1f461cac1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050829"
---
# <a name="application-provisioning-in-quarantine-status"></a>Anwendungsbereitstellung im Quarantänestatus

Der Azure AD-Bereitstellungsdienst überwacht die Integrität Ihrer Konfiguration. Außerdem werden fehlerhafte Apps in den Status „Quarantäne“ versetzt. Wenn die meisten oder alle an das Zielsystem gerichteten Aufrufe dauerhaft aufgrund eines Fehlers nicht erfolgreich sind, wird der Bereitstellungsauftrag in den Quarantänestatus versetzt. Ein Beispiel für einen Fehler ist eine Fehlermeldung, die aufgrund ungültiger Administratoranmeldeinformationen empfangen wurde.

In der Quarantäne geschieht Folgendes:
- Die Häufigkeit der inkrementellen Zyklen wird allmählich auf einmal pro Tag verringert.
- Die Quarantäne für den Bereitstellungsauftrag wird aufgehoben, nachdem alle Fehler behoben wurden, und der nächste Synchronisierungszyklus wird gestartet. 
- Wenn der Bereitstellungsauftrag länger als vier Wochen in Quarantäne verbleibt, wird er deaktiviert (nicht mehr ausgeführt).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Wie kann ich prüfen, ob sich meine Anwendung in Quarantäne befindet?

Es gibt drei Möglichkeiten, um zu überprüfen, ob sich eine Anwendung in Quarantäne befindet:

- Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Unternehmensanwendungen** > &lt;*Anwendungsname*&gt; > **Bereitstellung**, und suchen Sie in der Statusanzeige nach einer Quarantänenachricht.   

  ![Statusanzeige für die Bereitstellung mit dem Quarantänestatus](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Überwachungsprotokolle**, und legen Sie den Filter auf **Aktivität: Quarantäne** fest, und überprüfen Sie den Quarantäneverlauf. Die Ansicht in der Statusanzeige, wie oben beschrieben, zeigt, ob die Bereitstellung unter Quarantäne steht. In den Überwachungsprotokollen wird der Quarantäneverlauf für eine Anwendung angezeigt. 

- Verwenden Sie die Microsoft Graph-Anforderung [GET synchronizationJob](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true), um den Status des Bereitstellungsauftrags programmgesteuert abzurufen:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Prüfen Sie Ihren Posteingang. Wird eine Anwendung unter Quarantäne gestellt, wird eine einmalige Benachrichtigungs-E-Mail gesendet. Wenn sich der Grund für die Quarantäne ändert, wird eine aktualisierte E-Mail mit dem neuen Grund für die Quarantäne gesendet. Wenn keine E-Mail angezeigt wird:

  - Stellen Sie sicher, dass Sie in der Bereitstellungskonfiguration für die Anwendung eine gültige **Benachrichtigungs-E-Mail** angegeben haben.
  - Stellen Sie sicher, dass im Posteingang für Benachrichtigungs-E-Mails keine Spamfilterung aktiviert ist.
  - Stellen Sie sicher, dass Sie keine E-Mails abbestellt haben.
  - Überprüfen Sie die E-Mails von `azure-noreply@microsoft.com`.

## <a name="why-is-my-application-in-quarantine"></a>Warum befindet sich meine Anwendung in Quarantäne?

|BESCHREIBUNG|Empfohlene Maßnahme|
|---|---|
|**SCIM-Konformitätsproblem:** Anstelle der erwarteten Antwort „HTTP/200 OK“ wurde die Antwort „HTTP/404 Nicht gefunden“ zurückgegeben. In diesem Fall hat der Azure AD-Bereitstellungsdienst eine Anforderung an die Zielanwendung gesendet und eine unerwartete Antwort empfangen.|Überprüfen Sie die Administratoranmeldeinformationen. Überprüfen Sie, ob die Anwendung das Angeben der Mandanten-URL erfordert, und stellen Sie sicher, dass die URL korrekt ist. Wenn Sie kein Problem feststellen können, wenden Sie sich an den Anwendungsentwickler, um sicherzustellen, dass der Dienst SCIM-konform ist. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Ungültige Anmeldeinformationen:** Beim Versuch, den Zugriff auf die Zielanwendung zu autorisieren, wurde eine Antwort von der Zielanwendung empfangen, derzufolge die angegebenen Anmeldeinformationen ungültig sind.|Navigieren Sie auf der Benutzeroberfläche für die Bereitstellungskonfiguration zum Abschnitt „Administratoranmeldeinformationen“, und autorisieren Sie den Zugriff noch mal mit gültigen Anmeldeinformationen. Wenn sich die Anwendung im Katalog befindet, sehen Sie sich das Tutorial zur Anwendungskonfiguration an, um etwaige weitere erforderliche Schritte auszuführen.|
|**Doppelte Rollen:** Rollen, die aus bestimmten Anwendungen wie Salesforce oder Zendesk importiert werden, müssen eindeutig sein. |Navigieren Sie im Azure-Portal zum [Manifest](../develop/reference-app-manifest.md) der Anwendung, und entfernen Sie die doppelte Rolle.|

 Eine Microsoft Graph-Anforderung zum Abrufen des Status des Bereitstellungsauftrags zeigt den folgenden Grund für die Quarantäne an:
- `EncounteredQuarantineException` gibt an, dass ungültige Anmeldeinformationen angegeben wurden. Der Bereitstellungsdienst kann keine Verbindung zwischen dem Quellsystem und dem Zielsystem herstellen.
- `EncounteredEscrowProportionThreshold` gibt an, dass die Bereitstellung den hinterlegten Schwellenwert überschritten hat. Dieser Zustand tritt auf, wenn bei mehr als 40 % der Bereitstellungsereignisse Fehler aufgetreten sind. Weitere Informationen finden Sie weiter unten unter Details zum Hinterlegungsschwellenwert.
- `QuarantineOnDemand` bedeutet, dass wir ein Problem bei Ihrer Anwendung erkannt und sie manuell in den Quarantänestatus versetzt haben.

**Hinterlegungsschwellenwerte**

Wenn der proportionale Hinterlegungsschwellenwert erreicht wird, wird der Bereitstellungsauftrag unter Quarantäne gestellt. Diese Logik unterliegt Änderungen, funktioniert aber ungefähr wie unten beschrieben: 

Ein Auftrag kann unabhängig von der Anzahl von Fehlern, z. B. in Verbindung mit Administratoranmeldeinformationen oder SCIM-Konformität, unter Quarantäne gestellt werden. Im Allgemeinen sind 5.000 Fehler jedoch das Minimum, ab dem bewertet wird, ob die Quarantäne aufgrund zu vieler Fehler angewendet werden soll. Beispielsweise würde ein Auftrag mit 4.000 Fehlern nicht unter Quarantäne gestellt. Ein Auftrag mit 5.000 Fehlern würde jedoch eine Auswertung auslöst. Bei einer Auswertung gelten folgende Kriterien:  
- Wenn bei mehr als 40 % der Bereitstellungsereignisse Fehler auftreten oder mehr als 40.000 Fehler auftreten, wird der Bereitstellungsauftrag unter Quarantäne gestellt. Verweisfehler werden nicht als Teil des Schwellenwerts von 40 % oder des 40.000-Schwellenwerts gezählt. Ein Fehler beim Aktualisieren eines Managers oder eines Gruppenmitglieds ist beispielsweise ein Verweisfehler.
- Ein Auftrag, bei dem die Bereitstellung bei 45.000 Benutzern erfolglos verlief, würde zur Quarantäne führen, weil er den 40.000-Schwellenwert überschreitet.
- Ein Auftrag, bei dem die Bereitstellung bei 30.000 Benutzern erfolglos verlief und bei 5.000 erfolgreich war, würde zu einer Quarantäne führen, weil er den Schwellenwert von 40 % und einem Minimum von 5.000 überschreitet.
- Ein Auftrag mit 20.000 Fehlern und 100.000 Erfolgen würde nicht unter Quarantäne gestellt, weil er den Fehlerschwellenwert von 40 % oder den Höchstwert von 40.000 Fehlern nicht überschreitet.  
- Bei einem absoluten Schwellenwert von 60.000 Fehlern werden sowohl Verweis- als auch Nicht-Verweisfehler berücksichtigt. Beispiel: Die Bereitstellung konnte für 40.000 Benutzer nicht durchgeführt werden, und bei 21.000 Managerupdates ist ein Fehler aufgetreten. Die Summe beträgt 61.000 Fehler und überschreitet den Grenzwert von 60.000.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Wie kann ich dafür sorgen, dass meine Anwendung aus der Quarantäne entfernt wird?

Beheben Sie zuerst das Problem, das dazu geführt hat, dass die Anwendung unter Quarantäne gestellt wurde.

- Überprüfen Sie die Bereitstellungseinstellungen der Anwendung, um sicherzustellen, dass Sie [gültige Administratoranmeldeinformationen eingegeben](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning) haben. Azure AD muss eine Vertrauensstellung mit der Zielanwendung einrichten. Stellen Sie sicher, dass Sie gültige Anmeldeinformationen eingegeben haben und Ihr Konto über die erforderlichen Berechtigungen verfügt.

- Überprüfen Sie die [Bereitstellungsprotokolle](../reports-monitoring/concept-provisioning-logs.md), um weiter zu untersuchen, welche Fehler zur Quarantäne geführt haben, und beheben Sie den Fehler. Navigieren Sie im Abschnitt **Aktivität** zu **Azure Active Directory** &gt; **Unternehmens-Apps** &gt; **Bereitstellungsprotokolle (Vorschau)** .

Nachdem Sie das Problem behoben haben, können Sie den Bereitstellungsauftrag neu starten. Durch bestimmte Änderungen an den Bereitstellungseinstellungen (wie Attributzuordnungen oder Bereichsfilter) der Anwendung wird die Bereitstellung automatisch neu gestartet. Die Statusanzeige auf der Seite **Bereitstellung** der Anwendung gibt an, wann die Bereitstellung zuletzt gestartet wurde. Wenn Sie den Bereitstellungsauftrag manuell neu starten müssen, verwenden Sie eine der folgenden Methoden:  

- Verwenden Sie das Azure-Portal, um den Bereitstellungsauftrag neu zu starten. Klicken Sie auf der **Bereitstellungsseite** der Anwendung auf **Bereitstellung erneut starten**. Durch diese Aktion wird der Bereitstellungsdienst vollständig neu gestartet. Dieser Vorgang kann einige Zeit in Anspruch nehmen. Ein vollständiger Startzyklus wird erneut ausgeführt. Dabei werden Hinterlegungen gelöscht, die App wird aus der Quarantäne entfernt, und alle Wasserzeichen werden gelöscht. Der Dienst wertet dann alle Benutzer im Quellsystem nochmal aus und ermittelt, ob sie für die Bereitstellung zulässig sind. Dies kann sich als nützlich erweisen, wenn sich Ihre Anwendung wie in diesem Artikel beschrieben gerade in Quarantäne befindet oder wenn Sie eine Änderung an den Attributzuordnungen vornehmen müssen. Beachten Sie, dass die Ausführung des Startzyklus aufgrund der Anzahl der auszuwertenden Objekte länger dauert als herkömmliche inkrementelle Zyklen. Weitere Informationen zur Leistung von Startzyklen und inkrementellen Zyklen finden Sie [hier](application-provisioning-when-will-provisioning-finish-specific-user.md).

- Verwenden Sie Microsoft Graph, um [den Bereitstellungsauftrag neu zu starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true). Sie haben vollständige Kontrolle über die Elemente, die Sie neu starten. Sie können auswählen, ob Hinterlegungen (zum Neustarten des Hinterlegungszählers, der in Richtung Quarantänestatus läuft) gelöscht, die Quarantäne (zum Entfernen der Anwendung aus der Quarantäne) oder die Wasserzeichen gelöscht werden soll(en). Verwenden Sie die folgende Anforderung:

```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Ersetzen Sie „{ID}“ durch den Wert der Anwendungs-ID und „{jobId}“ durch die [ID des Synchronisierungsauftrags](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
