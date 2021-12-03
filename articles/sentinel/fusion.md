---
title: Erweiterte Erkennung von mehrstufigen Angriffen in Microsoft Sentinel
description: Verwenden Sie die Fusion-Technologie in Microsoft Sentinel, um die „Alarmmüdigkeit“ zu reduzieren und verwertbare Incidents zu erstellen, die auf der erweiterten Erkennung von mehrstufigen Angriffen basieren.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fa4fea5502f2e034aa6b5ba3f672cf8012dcd5a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520045"
---
# <a name="advanced-multistage-attack-detection-in-microsoft-sentinel"></a>Erweiterte Erkennung von mehrstufigen Angriffen in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
> Einige Fusion-Erkennungen (weiter unten aufgeführt) befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel verwendet Fusion, ein Korrelations-Modul, das auf skalierbaren Algorithmen von Maschinellem Lernen basiert, um mehrstufige Angriffe (auch als erweiterte persistente Bedrohungen oder APT bezeichnet) automatisch zu erkennen, indem Kombinationen aus anomalem Verhalten und verdächtigen Aktivitäten identifiziert werden, die in verschiedenen Phasen der Kette auftreten können. Auf der Grundlage dieser Entdeckungen generiert Microsoft Sentinel Incidents, die auf andere Weise nur schwer abgefangen werden können. Diese Incidents umfassen mindestens zwei Warnungen oder Aktivitäten. Standardmäßig weisen diese Incidents ein geringes Volumen, eine hohe Qualität und einen hohen Schweregrad auf.

Diese Erkennungstechnologie ist für Ihre Umgebung angepasst und bewirkt nicht nur eine Reduzierung der [False Positive](false-positives.md)-Rate, sondern kann Angriffe auch mit eingeschränkten oder fehlenden Informationen erkennen.

Da Fusion mehrere Signale von verschiedenen Produkten korreliert, um erweiterte mehrstufige Angriffe zu erkennen, werden erfolgreiche Erkennungen von Fusion als **Fusion-Vorfälle** auf der Seite Microsoft Sentinel **Vorfälle** und nicht als **Warnungen** angezeigt und in der Tabelle *Vorfälle* in **Protokollen** und nicht in der Tabelle *Sicherheitswarnungen* gespeichert.

### <a name="configure-fusion"></a>Konfigurieren von Fusion

Fusion ist standardmäßig in Microsoft Sentinel als [ Analyseregel](detect-threats-built-in.md#view-built-in-detections) namens **Erweiterte mehrstufige Angriffserkennung** aktiviert. Sie können den Status des Filters anzeigen und ändern, Quellsignale so konfigurieren, dass sie in das Fusion ML-Modell aufgenommen werden, oder bestimmte Erkennungsmuster von der Erkennung durch Fusion ausschließen, die möglicherweise nicht auf Ihre Umgebung anwendbar sind. Erfahren Sie hier, wie Sie [den Fusion-Filter konfigurieren](configure-fusion-rules.md).

> [!NOTE]
> Microsoft Sentinel verwendet derzeit Verlaufsdaten von 30 Tagen, um die Algorithmen für das Maschinelle Lernen des Fusion Moduls zu trainieren. Diese Daten werden immer mit den Schlüsseln von Microsoft verschlüsselt, wenn sie die Pipeline für maschinelles Lernen durchlaufen. Die Trainingsdaten werden jedoch nicht mit [vom Kunden verwalteten Schlüsseln (Customer Managed Keys, CMK)](customer-managed-keys.md) verschlüsselt, wenn Sie CMK in Ihrem Microsoft Sentinel-Arbeitsbereich aktiviert haben. So deaktivieren Sie Fusion: Navigieren Sie zu **Microsoft Sentinel** \> **Konfiguration** \> **Analytics \> Aktive Regeln**, klicken Sie mit der rechten Maustaste auf die Regel **Advanced Multistage Attack Detection** („Erweiterte mehrstufige Angriffserkennung“), und wählen Sie **Deaktivieren** aus.

## <a name="fusion-for-emerging-threats"></a>Fusion für neue Bedrohungen

> [!IMPORTANT]
>
> - Die fusionsbasierte Erkennung für neue Bedrohungen befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Die Menge der Sicherheitsereignisse wächst weiter, und der Umfang und die Raffinesse von Angriffen nehmen ständig zu. Zwar können wir die bekannten Angriffsszenarien definieren, doch wie sieht es eigentlich mit den neuen und noch unbekannten Bedrohungen in Ihrer Umgebung aus?  

Das von ML unterstützte Microsoft Sentinel Fusion Modul hilft Ihnen, die **neuen und unbekannten Bedrohungen** in Ihrer Umgebung zu finden, indem sie eine **erweiterte ML-Analyse** anwenden und **einen größeren Bereich anomaler Signale** korrelieren und gleichzeitig die Warnungsmüdigkeit gering halten.

Die ML-Algorithmen des Fusion Moduls lernen ständig aus erfolgten Angriffen und wenden Analysen basierend auf der Meinung von Sicherheitsanalysten an. Es kann daher zuvor unerkannte Bedrohungen von Millionen anomaler Verhaltensweisen in der gesamten Kette in Ihrer Umgebung erkennen, wodurch Sie den Angreifern einen Schritt voraus sein können.

**Fusion für neue Bedrohungen** unterstützt die Datensammlung und -analyse aus den folgenden Quellen:

- [Standardmäßige Erkennung von Anomalien](soc-ml-anomalies.md)
- Warnungen von Microsoft-Produkten:
  - Azure Active Directory Identity Protection
  - Microsoft Defender für Cloud
  - Microsoft Defender für IoT
  - Microsoft 365 Defender
  - Microsoft Defender für Cloud-Apps
  - Microsoft Defender für den Endpunkt
  - Microsoft Defender for Identity
  - Microsoft Defender für Office 365
- [**Warnungen aus Filtern für geplante**](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections)Analysen, und zwar sowohl [integrierte](detect-threats-built-in.md#scheduled) als auch [von Ihren Sicherheitsanalysten erstellte.](detect-threats-custom.md) Analysefilter müssen Informationen über die Angriffskette (Taktiken) und die Zuordnung der Einheit enthalten, um von Fusion verwendet werden zu können.

Sie müssen nicht unbedingt *alle* oben aufgeführten Datenquellen verbunden haben, damit Fusion für neue Bedrohungen funktioniert. Doch desto mehr Datenquellen Sie verknüpft haben, je umfassender ist die Abdeckung, und desto mehr Bedrohungen wird Fusion entdecken.

Wenn die Korrelationen des Fusion Moduls zur Erkennung einer neuen Bedrohung führen, wird in der Tabelle **Vorfälle** in Ihrem Microsoft Sentinel-Arbeitsbereich ein Vorfall mit hohem Schweregrad mit dem Titel "*Potentielle mehrstufige Angriffsaktivitäten von Fusion erkannt*" generiert.

## <a name="fusion-for-ransomware"></a>Fusion für Ransomware

Das Microsoft Sentinel Fusion Modul von Azure generiert einen Vorfall, wenn mehrere Warnungen verschiedener Typen aus den folgenden Datenquellen erkannt werden, und entscheidet, dass sie mit Ransomware-Aktivitäten in Zusammenhang stehen könnten:

- [Microsoft Defender für Cloud](connect-azure-security-center.md)
- [Microsoft Defender für den Endpunkt](./data-connectors-reference.md#microsoft-defender-for-endpoint)
- [Microsoft Defender for Identity](./data-connectors-reference.md#microsoft-defender-for-identity)
- [Microsoft Defender für Cloud-Apps](./data-connectors-reference.md#microsoft-defender-for-cloud-apps)
- [Microsoft Sentinel-Regeln für geplante Analysen](detect-threats-built-in.md#scheduled) Fusion berücksichtigt nur Filter für geplante Analysen mit Taktikinformationen und zugeordneten Einheiten.

Solche Fusion-Incidents heißen **Mehrere Warnungen, die sich möglicherweise auf erkannte Ransomware-Aktivität beziehen**, und werden generiert, wenn relevante Warnungen in einem bestimmten Zeitrahmen erkannt werden und mit den Phasen **Ausführung** und **Umgehen von Verteidigungsmaßnahmen** eines Angriffs verknüpft sind.

Beispielsweise generiert Microsoft Sentinel einen Incident für mögliche Ransomware-Aktivitäten, wenn die folgenden Warnungen in einem bestimmten Zeitrahmen auf demselben Host ausgelöst werden:

| Warnung | `Source` | Schweregrad |
| ----- | ------ | -------- |
| **Windows Fehler- und Warnereignisse** | Microsoft Sentinel-Regeln für geplante Analysen | Information |
| **Ransomware ‘GandCrab‘ wurde verhindert** | Microsoft Defender für Cloud | mittel |
| **‘Emotet‘-Schadsoftware wurde erkannt** | Microsoft Defender für den Endpunkt | Information |
| **‘Tofsee‘ hinten erkannt** | Microsoft Defender für Cloud | niedrig |
| **‘Parite‘-Schadsoftware wurde erkannt** | Microsoft Defender für den Endpunkt | Information |

## <a name="scenario-based-fusion-detections"></a>Szenariobasierte Erkennungen durch Fusion

Im folgenden Abschnitt werden die Arten von [szenariobasierten mehrstufigen Angriffen](fusion-scenario-reference.md) nach Bedrohungsklassifizierung aufgelistet, die Microsoft Sentinel mit dem Fusion-Korrelations-Filter erkennt.

Um diese Fusion-gestützten Angriffserkennungsszenarien zu ermöglichen, müssen die zugehörigen Datenquellen in Ihrem Log Analytics-Arbeitsbereich erfasst sein. Wählen Sie die Links in der folgenden Tabelle aus, um mehr über die einzelnen Szenarien und die zugehörigen Datenquellen zu erfahren.

> [!NOTE]
> Einige dieser Szenarios befinden sich in der **VORSCHAU**. Diese sind entsprechend gekennzeichnet.

| Bedrohungsklassifizierung  | Szenarien  |
| -------- | -------- |
| **Missbrauch von Computeressourcen**      | <ul><li>(VORSCHAU) [Mehrere VM-Erstellungsaktivitäten *nach* verdächtiger Azure Active Directory Anmeldung](fusion-scenario-reference.md#multiple-vm-creation-activities-following-suspicious-azure-active-directory-sign-in) |
| **Zugriff auf Anmeldeinformationen**           | <ul><li>(VORSCHAU) [Zurücksetzen mehrerer Kennwörter durch den Benutzer *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#multiple-passwords-reset-by-user-following-suspicious-sign-in) <li>(VORSCHAU) [Verdächtige Anmeldung *mit* erfolgreicher Anmeldung bei Palo Alto VPN<br>mittels IP mit mehreren fehlgeschlagenen Azure AD Anmeldungen](fusion-scenario-reference.md#suspicious-sign-in-coinciding-with-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins) |
| **Abgreifen von Anmeldeinformation**       | <ul><li>[Ausführung des Tools zum Diebstahl schädlicher Anmeldeinformationen *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#malicious-credential-theft-tool-execution-following-suspicious-sign-in)    <li>[Verdacht auf Diebstahl von Anmeldeinformationen *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#suspected-credential-theft-activity-following-suspicious-sign-in)      |
| **Crypto-mining**               | <ul><li>[Crypto-Mining-Aktivität *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#crypto-mining-activity-following-suspicious-sign-in)          |
| **Datenvernichtung**            | <ul><li>[Massenlöschung von Dateien *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#mass-file-deletion-following-suspicious-azure-ad-sign-in)     <li>(VORSCHAU) [Massenlöschung von Dateien *nach* erfolgreicher Azure AD Anmeldung durch <br> von einer Cisco Firewall-Appliance blockierte IP-Adresse](fusion-scenario-reference.md#mass-file-deletion-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)        <li>(VORSCHAU) [Massenlöschung von Dateien *nach* erfolgreicher Anmeldung bei Palo Alto VPN <br>mittels IP mit mehreren fehlgeschlagenen Azure AD Anmeldungen](fusion-scenario-reference.md#mass-file-deletion-following-successful-sign-in-to-palo-alto-vpn-by-ip-with-multiple-failed-azure-ad-sign-ins)        <li>(VORSCHAU) [Verdächtige E-Mail-Löschaktivität *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-email-deletion-activity-following-suspicious-azure-ad-sign-in) |
| **Daten-Exfiltration**           | <ul><li>(VORSCHAU) [E-Mail-Weiterleitungsaktivitäten *nach* neuer Administratorkontoaktivität, die zuletzt nicht angezeigt wurde](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently)      <li>[Massendownload von Dateien *nach*  verdächtige Azure AD Anmeldung](fusion-scenario-reference.md#mass-file-download-following-suspicious-azure-ad-sign-in)       <li>VORSCHAU) [Massendownload von Dateien  *nach* erfolgreicher Azure AD Anmeldung mittels <br> einer durch eine Cisco Firewall-Appliance blockierte IP-Adresse](fusion-scenario-reference.md#mass-file-download-following-successful-azure-ad-sign-in-from-ip-blocked-by-a-cisco-firewall-appliance)       <li>(VORSCHAU) [Massendownload von Dateien *mit* SharePoint Dateivorgang von zuvor nicht verwendeter IP-Adresse](fusion-scenario-reference.md#mass-file-download-coinciding-with-sharepoint-file-operation-from-previously-unseen-ip)        <li>[Massenfreigabe von Dateien *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#mass-file-sharing-following-suspicious-azure-ad-sign-in)         <li>(VORSCHAU) [Mehrere Aktivitäten zur Freigabe von Power BI-Berichten *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#multiple-power-bi-report-sharing-activities-following-suspicious-azure-ad-sign-in)         <li>[Office 365 Postfach Exfiltration *nach einer* verdächtigen Azure AD Anmeldung](fusion-scenario-reference.md#office-365-mailbox-exfiltration-following-a-suspicious-azure-ad-sign-in)        <li>(VORSCHAU) [SharePoint Dateivorgang von einer zuvor nicht erkannten IP-Adresse *nach* der Erkennung von Schadsoftware](fusion-scenario-reference.md#sharepoint-file-operation-from-previously-unseen-ip-following-malware-detection)         <li>(VORSCHAU) [Festlegung verdächtiger Regeln zur Posteingangsänderung *nach* einer verdächtigen Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in)        <li>(VORSCHAU) [Verdächtige Freigabe von Power BI-Berichten *nach* einer verdächtigen Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-power-bi-report-sharing-following-suspicious-azure-ad-sign-in)  |
| **Denial of Service**           | <ul><li>(VORSCHAU) [Mehrere Aktivitäten zur Löschung von VMs *nach* einer verdächtigen Azure AD Anmeldung](fusion-scenario-reference.md#multiple-vm-deletion-activities-following-suspicious-azure-ad-sign-in)          |
| **Seitwärtsbewegung**            | <ul><li>[Office 365 Identitätswechsel *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#office-365-impersonation-following-suspicious-azure-ad-sign-in)      <li>(VORSCHAU) [Festlegung verdächtiger Regeln zur Posteingangsänderung *nach* einer verdächtigen Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-inbox-manipulation-rules-set-following-suspicious-azure-ad-sign-in-1)        |
| **Böswillige administrative Aktivität**     | <ul><li>(VORSCHAU) [Verdächtige Cloud-App-Administratoraktivität *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-cloud-app-administrative-activity-following-suspicious-azure-ad-sign-in)    <li>(VORSCHAU) [E-Mail-Weiterleitungsaktivitäten *nach* neuer Administratorkontoaktivität, die zuletzt nicht angezeigt wurde](fusion-scenario-reference.md#mail-forwarding-activities-following-new-admin-account-activity-not-seen-recently-1)          |
| **Böswillige Ausführung <br> mit legitimem Prozess**    | <ul><li>(VORSCHAU) [PowerShell hat eine verdächtige Netzwerkverbindung hergestellt, *gefolgt von* <br>anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr](fusion-scenario-reference.md#powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>(VORSCHAU) [Verdächtige WMI-Remoteausführung *gefolgt von*<br> anomalem durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr](fusion-scenario-reference.md#suspicious-remote-wmi-execution-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)   <li>[Verdächtige PowerShell-Befehlszeile *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#suspicious-powershell-command-line-following-suspicious-sign-in)          |
| **Malware C2 oder Download**      | <ul><li>(VORSCHAU) [Von Fortinet erkanntes Beaconmuster nach mehreren fehlgeschlagenen Benutzeranmeldungen bei einem Dienst](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-multiple-failed-user-sign-ins-to-a-service)     <li>(VORSCHAU) [Von Fortinet erkanntes Beaconmuster *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#beacon-pattern-detected-by-fortinet-following-suspicious-azure-ad-sign-in)       <li>(VORSCHAU) [Netzwerkanforderung an den TOR-Anonymisierungsdienst *gefolgt von* <br> anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr](fusion-scenario-reference.md#network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)       <li>(VORSCHAU) [ Ausgehende Verbindung mit IP mit einem Verlauf nicht autorisierter Zugriffsversuche, *gefolgt von*  <br>anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr](fusion-scenario-reference.md#outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)    |
| **Persistenz**                 | <ul><li>(VORSCHAU) [Seltene Anwendungseinwilligung *nach* verdächtiger Anmeldung](fusion-scenario-reference.md#rare-application-consent-following-suspicious-sign-in)         |
| **Ransomware**                  | <ul><li>[Ransomware-Ausführung *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#ransomware-execution-following-suspicious-azure-ad-sign-in)          |
| **Remoteausnutzung**         | <ul><li>(VORSCHAU) [Verdacht der Nutzung eines Angriffs-Frameworks, *gefolgt von* <br> anomalem, durch die Palo Alto Networks-Firewall gekennzeichnetem Datenverkehr](fusion-scenario-reference.md#suspected-use-of-attack-framework-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall)          |
| **Ressourcenübernahme**          | <ul><li>(VORSCHAU) [ Verdächtige Ressourcen-/Ressourcengruppenbereitstellung durch einen zuvor unbekannten Aufrufer<br> *nach* verdächtiger Azure AD Anmeldung](fusion-scenario-reference.md#suspicious-resource--resource-group-deployment-by-a-previously-unseen-caller-following-suspicious-azure-ad-sign-in)          |
|

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur erweiterten Erkennung mehrstufiger Angriffe durch Fusion:

- Erfahren Sie mehr über [die szenariobasierten Angriffserkennungen durch Fusion](fusion-scenario-reference.md).
- Hier erfahren Sie, wie Sie [die Filter für Fusion konfigurieren.](configure-fusion-rules.md)

Nachdem Sie nun mehr über die erweiterte Erkennung von mehrstufigen Angriffen erfahren haben, ist für Sie ggf. die folgende Schnellstartanleitung interessant. Darin wird veranschaulicht, wie Sie Einblicke in Ihre Daten und potenzielle Bedrohungen erhalten: [Erste Schritte mit Microsoft Sentinel](get-visibility.md).

Wenn Sie bereit sind, die Vorfälle zu untersuchen, die für Sie erstellt werden, lesen Sie das folgende Tutorial: [Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md).
