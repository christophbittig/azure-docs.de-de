---
title: Berichtsarbeitsmappe zu riskanten IP-Adressen von Azure AD Connect Health mit AD FS | Microsoft-Dokumentation
description: Beschreibt den Bericht von Azure AD Connect Health mit AD FS über riskante IP-Adressen mit Azure Monitor-Arbeitsmappen.
services: active-directory
documentationcenter: ''
ms.reviewer: ''
author: billmath
manager: karen444
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/14/2021
ms.author: billmath
ms.custom: ''
ms.collection: ''
ms.openlocfilehash: 8463da51788c7faa680f42aaeb1999c174419366
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478930"
---
# <a name="risky-ip-report-workbook"></a>Berichtsarbeitsmappe zu riskanten IP-Adressen 
> [!NOTE]
> Um die Berichtsarbeitsmappe zu riskanten IP-Adressen zu verwenden, müssen Sie ADFSSignInLogs auf dem Blatt „Diagnoseeinstellungen“ aktivieren. Dies ist ein Log Analytics-Stream mit AD FS-Anmeldungen, der über Connect Health an Azure AD gesendet wird. Weitere Informationen zu AD FS-Anmeldungen in Azure AD finden Sie hier in unserer Dokumentation.

AD FS-Kunden können Endpunkte für die Kennwortauthentifizierung für den Zugriff über das Internet verfügbar machen, um Authentifizierungsdienste für Endbenutzer bereitzustellen, damit diese auf SaaS-Anwendungen wie Microsoft 365 zugreifen können. In diesem Fall ist es möglich, dass ein böswilliger Benutzer versucht, sich an Ihrem AD FS-System anzumelden, um das Kennwort eines Endbenutzers zu erraten und Zugriff auf Anwendungsressourcen zu erhalten. AD FS verfügt seit der Einbindung in Windows Server 2012 R2 über die Funktion zum Sperren von Extranet-Konten, um diese Arten von Angriffen zu verhindern. Falls Sie eine frühere Version verwenden, empfehlen wir Ihnen dringend, Ihr AD FS-System auf Windows Server 2016 zu aktualisieren. <br />

Außerdem ist es möglich, dass von einer einzelnen IP-Adresse versucht wird, mehrere Anmeldungen für mehrere Benutzer durchzuführen. In diesen Fällen kann es sein, dass die Anzahl von Versuchen pro Benutzer unter dem Schwellenwert für den Schutz durch Kontosperrung in AD FS liegt. Azure AD Connect Health enthält jetzt einen „Bericht über riskante IP-Adressen“, mit dem dieser Zustand erkannt wird und Administratoren benachrichtigt werden. Hier sind die wichtigsten Vorteile dieses Berichts angegeben: 
- Erkennung von IP-Adressen, für die ein Schwellenwert für fehlgeschlagene kennwortbasierte Anmeldungen überschritten wird
- Unterstützung von fehlgeschlagenen Anmeldungen aufgrund eines fehlerhaften Kennworts oder eines Extranet-Sperrzustands
- Unterstützt das Aktivieren von Warnungen über Azure-Warnungen
- Anpassbare Einstellungen für den Schwellenwert, die der Sicherheitsrichtlinie einer Organisation entsprechen
- Anpassbare Abfragen und erweiterte Visualisierungen zur weiteren Analyse
- Erweiterte Funktionalität gegenüber dem vorherigen Bericht zu riskanten IP-Adressen, der nach dem 24. Januar 2022 als veraltet gilt

## <a name="requirements"></a>Requirements (Anforderungen)
1.  Connect Health für AD FS ist installiert und wurde auf den neuesten Agent aktualisiert.
2.  Ein Log Analytics-Arbeitsbereich, für den der Stream ADFSSignInLogs aktiviert ist
3.  Berechtigungen zum Verwenden der Azure AD-Monitor-Arbeitsmappen Für die Verwendung von Arbeitsmappen benötigen Sie Folgendes:
- Einen Azure Active Directory-Mandanten mit einer Premium-Lizenz (P1 oder P2).
- Zugriff auf einen Log Analytics-Arbeitsbereich und die folgenden Rollen in Azure AD (bei Zugriff auf Log Analytics über das Azure AD-Portal): Sicherheitsadministrator, Sicherheitsleseberechtigter, Berichtsleser, globaler Administrator


## <a name="what-is-in-the-report"></a>Inhalt des Berichts
Die Berichtsarbeitsmappe zu riskanten IP-Adressen basiert auf Daten im ADFSSignInLogs-Stream und verfügt über bereits vorhandene Abfragen, mit denen Sie riskante IP-Adressen schnell visualisieren und analysieren können. Die Parameter können konfiguriert und an Schwellenwerte angepasst werden. Die Arbeitsmappe kann auch basierend auf Abfragen konfiguriert werden, und jede Abfrage kann basierend auf den Anforderungen der Organisation aktualisiert und geändert werden.

Die Arbeitsmappe für riskante IP-Adressen analysiert Daten aus ADFSSignInLogs, um Sie bei der Erkennung von Spray- oder Brute-Force-Angriffen auf Kennwörter zu unterstützen. Die Arbeitsmappe umfasst zwei Teile. Im ersten Teil, „Analyse riskanter IP-Adressen“, werden riskante IP-Adressen basierend auf festgelegten Fehlerschwellenwerten und der Länge des Erkennungsfensters identifiziert. Der zweite Teil enthält die Anmeldedetails und die Fehleranzahl für ausgewählte IP-Adressen.

[![Screenshot einer Ansicht der Arbeitsmappe mit Standorten](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/workbook-template-overview-1.png#lightbox)

- Die Arbeitsmappe zeigt eine Kartenvisualisierung und eine Regionsaufschlüsselung für eine schnelle Analyse riskanter IP-Standorte.
- Die Tabelle mit Details zu riskanten IP-Adressen entspricht der Funktionalität des früheren Berichts zu riskanten IP-Adressen. Ausführliche Informationen zu den Feldern in der Tabelle finden Sie im Abschnitt unten.
- Die Zeitachse für riskante IP-Adressen zeigt eine schnelle Ansicht aller Anomalien oder Spitzen bei Anforderungen im zeitlichen Verlauf.
- Anmeldedetails und Fehleranzahl nach IP-Adresse bieten eine detaillierte gefilterte Ansicht nach IP-Adresse oder Benutzer, um die Detailtabelle zu erweitern. 

Jeder Eintrag in der Berichtstabelle über riskante IP-Adressen enthält aggregierte Informationen zu fehlerhaften AD FS-Anmeldeaktivitäten, für die der angegebene Schwellenwert überschritten wurde. Folgende Informationen sind enthalten: [![Screenshot des Berichts über riskante IP-Adressen mit hervorgehobenen Spaltenüberschriften](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/risky-ip-table-1.png#lightbox)

| Berichtselement | BESCHREIBUNG |
| ------- | ----------- |
| Startzeit des Erkennungsfensters | Zeigt den Zeitstempel basierend auf der lokalen Zeit im Azure-Portal an, wenn das Erkennungszeitfenster beginnt.<br /> Alle täglichen Ereignisse werden um Mitternacht (UTC) generiert. <br />Für stündliche Ereignisse wird der Zeitstempel auf den Anfang der Stunde gerundet. Sie finden die Startzeit der ersten Aktivität unter „firstAuditTimestamp“ in der exportierten Datei. |
| Länge des Erkennungsfensters | Zeigt den Typ des Erkennungszeitfensters an. Die Triggertypen der Aggregation sind „Pro Stunde“ und „Pro Tag“. Dies ist hilfreich beim Erkennen eines Brute-Force-Angriffs mit hoher Häufigkeit gegenüber einem langsamen Angriff, bei dem sich die Versuche auf den gesamten Tag verteilen. |
| IP-Adresse | Die einzelne riskante IP-Adresse, für die es entweder zu Anmeldeaktivitäten mit einem fehlerhaften Kennwort oder einer Extranetsperrung gekommen ist. Dies kann eine IPv4- oder eine IPv6-Adresse sein. |
| Fehleranzahl aufgrund eines falschen Kennworts (50.126) | Die Anzahl von Fehlern der Art „Falsches Kennwort“, die für die IP-Adresse während des Erkennungszeitfensters aufgetreten sind. Fehler der Art „Falsches Kennwort“ können für bestimmte Benutzer mehrfach auftreten. Beachten Sie, dass hierin keine Fehlversuche aufgrund von abgelaufenen Kennwörtern enthalten sind. |
| Fehleranzahl aufgrund einer Extranetsperre (30.030) | Die Anzahl von Fehlern der Art „Extranetsperre“, die für die IP-Adresse während des Erkennungszeitfensters aufgetreten sind. Die Fehler der Art „Extranetsperre“ können für bestimmte Benutzer mehrfach auftreten. Dies ist nur der Fall, wenn in AD FS (Versionen 2012 R2 oder höher) die Extranetsperre konfiguriert wurde. <b>Hinweis</b> Wir empfehlen Ihnen dringend, dieses Feature zu aktivieren, wenn Sie Extranetanmeldungen mit Kennwörtern zulassen. |
| Eindeutiger Benutzername bei Versuch | Die Anzahl von eindeutigen Benutzerkonten, mit denen über die IP-Adresse während des Erkennungszeitfensters Versuche unternommen wurden. Hiermit kann unterschieden werden, ob es sich um einen Angriff eines einzelnen Benutzers oder von mehreren Benutzern handelt.  |

Filtern Sie den Bericht nach IP-Adresse oder Benutzername, um eine erweiterte Ansicht der Anmeldedetails für jedes Ereignis mit einer riskanten IP-Adresse anzuzeigen.

## <a name="load-balancer-ip-addresses-in-the-list"></a>Load Balancer-IP-Adressen in der Liste
Bei Anmeldeaktivitäten der Load Balancer-Aggregation ist ein Fehler aufgetreten, und der Warnungsschwellenwert wurde erreicht. Falls IP-Adressen des Lastenausgleichs angezeigt werden, sendet Ihr externer Lastenausgleich höchstwahrscheinlich die Client-IP-Adresse nicht, wenn er die Anforderung an den Webanwendungsproxy-Server übergibt. Konfigurieren Sie Ihren Lastenausgleich so, dass Forward-Client-IP-Adressen ordnungsgemäß weitergegeben werden. 

## <a name="configure-threshold-settings"></a>Konfigurieren von Schwellenwerteinstellungen
Der Schwellenwert für Warnungen kann über die „Schwellenwerteinstellungen“ aktualisiert werden. Zu Beginn ist der Schwellenwert im System standardmäßig festgelegt. Die Erkennungszeiten können in den Schwellenwerteinstellungen entweder nach Stunden oder Tagen festgelegt und in den Filtern angepasst werden.

[![Schwellenwertfilter](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/threshold-settings-1.png#lightbox)

| Schwellenwertelement | BESCHREIBUNG |
| --- | --- |
| Schwellenwert für die Fehler „Ungültiges Kennwort“ und „Extranetsperre“  | Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl falscher Kennwörter zusammen mit der Anzahl von Extranetsperren den Schwellenwert pro **Stunde oder Tag** überschreitet.|
| Schwellenwert für Extranetsperrungsfehler | Schwellenwerteinstellung zum Melden der Aktivität und Auslösen der Warnungsbenachrichtigung, wenn die Anzahl von Extranetsperren den Schwellenwert pro **Stunde oder Tag** überschreitet. Der Standardwert ist 50.|

Die Länge des Erkennungsfensters **Stunde oder Tag** kann über die Umschaltfläche oberhalb der Filter zum Anpassen von Schwellenwerten konfiguriert werden. 

## <a name="configure-notification-alerts-using-azure-monitor-alerts-through-the-azure-portal"></a>Konfigurieren von Benachrichtigungswarnungen mit Azure Monitor-Warnungen über das Azure-Portal:
[![Azure-Warnungsregel](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png)](./media/how-to-connect-health-adfs-risky-ip-workbook/azure-alerts-rule-1.png#lightbox)
1.  Suchen Sie im Azure-Portal auf der Suchleiste nach „Monitor“, um zum Azure Monitor-Dienst zu navigieren. Wählen Sie im linken Menü die Option „Warnungen“ und dann „+ Neue Warnungsregel“ aus. 
2.  Auf dem Blatt „Warnungsregel erstellen“:
   * Bereich: Klicken Sie auf „Ressource auswählen“, und wählen Sie Ihren Log Analytics-Arbeitsbereich aus, der den zu überwachenden ADFSSignInLogs-Stream enthält.
   * Bedingung: Klicken Sie auf „Bedingung hinzufügen“. Wählen Sie als Signaltyp „Protokoll“ und als Überwachungsdienst „Protokollanalyse“ aus. Wählen Sie „Benutzerdefinierte Protokollsuche“ aus. 

3. Konfigurieren Sie die Bedingung für das Auslösen der Warnung. Damit die E-Mail-Benachrichtigungen dem Connect Health-Bericht über riskante IP-Adressen entspricht, befolgen Sie die folgenden Anweisungen.
   * Kopieren Sie die folgende Abfrage, fügen Sie sie ein, und geben Sie die Schwellenwerte für die Fehleranzahl an. Diese Abfrage generiert die Anzahl von IP-Adressen, für die die festgelegten Fehlerschwellenwerte überschritten werden.

```
ADFSSignInLogs
| extend ErrorCode = ResultType
| where ErrorCode in ("50126", "300030")
| summarize badPasswordErrorCount = countif(ErrorCode == "50126"), extranetLockoutErrorCount = countif(ErrorCode == "300030") by IPAddress
| where extranetLockoutErrorCount > [TODO: put error count threshold here] 
```
Bei einem kombinierten Schwellenwert:
```
badPasswordErrorCount + extranetLockoutErrorCount > [TODO: put error count threshold here] // Customized thresholds
```

> [!NOTE]
> Gemäß der Warnungslogik wird die Warnung ausgelöst, wenn mindestens eine IP-Adresse aus der Anzahl der Extranetsperrungsfehler oder die kombinierte Anzahl für die Fehler „Ungültiges Kennwort“ und „Extranetsperre“ die festgelegten Schwellenwerte überschreitet. Sie können die Häufigkeit für die Auswertung der Abfrage für die Erkennung riskanter IP-Adressen auswählen.


## <a name="faq"></a>Häufig gestellte Fragen
**Warum enthält der Bericht IP-Adressen des Lastenausgleichs?**  <br />
Falls IP-Adressen des Lastenausgleichs angezeigt werden, sendet Ihr externer Lastenausgleich höchstwahrscheinlich die Client-IP-Adresse nicht, wenn er die Anforderung an den Webanwendungsproxy-Server übergibt. Konfigurieren Sie Ihren Lastenausgleich so, dass Forward-Client-IP-Adressen ordnungsgemäß weitergegeben werden. 

**Wie kann ich die IP-Adresse blockieren?**  <br />
Sie sollten identifizierte schädliche IP-Adressen der Firewall hinzufügen oder in Exchange blockieren.   <br />

**Warum werden in diesem Bericht keine Elemente angezeigt?** <br />
- Der Log Analytics-Stream ADFSSignInLogs ist in den Diagnoseeinstellungen nicht aktiviert.
- Es liegen keine fehlgeschlagenen Anmeldeaktivitäten vor, für die die Schwellenwerteinstellungen überschritten werden.
- Stellen Sie sicher, dass in Ihrer AD FS-Serverliste keine Warnung vom Typ „Integritätsdienst ist nicht aktuell” aktiv ist.  Informieren Sie sich über das [Durchführen der Problembehandlung für diese Warnung](how-to-connect-health-data-freshness.md).
- Überwachungen sind in AD FS-Farmen nicht aktiviert.

## <a name="next-steps"></a>Nächste Schritte
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
