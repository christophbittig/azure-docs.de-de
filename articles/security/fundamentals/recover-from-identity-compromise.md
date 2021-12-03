---
title: Verwenden Sie Microsoft- und Azure-Sicherheitsressourcen für Unterstützung bei der Wiederherstellung nach einer systemischen Identitätskompromittierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Microsoft- und Azure-Sicherheitsressourcen wie etwa Microsoft 365 Defender, Azure Sentinel und Azure Active Directory sowie Azure Security Center und Microsoft-Empfehlungen verwenden, um Ihr System vor kompromittierten Identitäten zu schützen, ähnlich wie beim Nobelium-Angriff (Solorigate) vom Dezember 2020.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2021
ms.author: bagol
ms.openlocfilehash: b858cb8d2830d305a90dfb8ecbda9fe2154e11f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323195"
---
# <a name="recovering-from-systemic-identity-compromise"></a>Wiederherstellen nach kompromittierter Systemidentität

In diesem Artikel werden die Microsoft-Ressourcen und -Empfehlungen für die Wiederherstellung nach einem systemischen Angriff auf eine Kompromittierung der Identität auf Ihr Unternehmen beschrieben, wie z. B. der [Nobelium](https://aka.ms/solorigate)-Angriff vom Dezember 2020.

Der Inhalt dieses Artikels basiert auf den bereitgestellten Anleitungen des Microsoft Detection and Response Teams (DART), das auf die Kompromittierungen reagiert und Kunden dabei hilft, cyberresilient zu werden. Weitere Anleitungen vom DART-Team finden Sie in der [Microsoft-Sicherheitsblogreihe](https://www.microsoft.com/security/blog/microsoft-detection-and-response-team-dart-blog-series/).

Viele Unternehmen sind zu einem cloudbasierten Ansatz übergewechselt, um die Sicherheit ihrer Identitäts- und Zugriffsverwaltung zu erhöhen. Ihr Unternehmen kann jedoch auch über lokale Systeme verfügen und unterschiedliche Methoden der Hybridarchitektur verwenden. In diesem Artikel wird bestätigt, dass sich systemische Identitätsangriffe auf die Cloud-, die lokalen und die Hybridsysteme auswirken, und bietet Empfehlungen und Referenzen für alle diese Umgebungen.

> [!IMPORTANT]
> Diese Informationen werden in der vorliegenden Form zur Verfügung gestellt und gelten als allgemeine Anleitung. Für die endgültige Festlegung darüber, wie diese Anleitung auf Ihre IT-Umgebung und Ihre Mandanten anzuwenden ist, muss Ihre individuelle Umgebung und Ihre Bedürfnisse berücksichtigt werden, die jeder Kunde am besten für sich beurteilen kann.
>

## <a name="about-systemic-identity-compromise"></a>Informationen zu kompromittierten Systemidentitäten

Ein systemischer Angriff auf ein Unternehmen, bei dem eine Identität kompromittiert wird, tritt auf, wenn ein Angreifer erfolgreich Fuß in die Verwaltung der Identitätsinfrastruktur eines Unternehmens gewinnen kann.

Wenn das in Ihrem Unternehmen passiert ist, befinden Sie sich in einem Wettlauf gegen den Angreifer, um Ihre Umgebung zu schützen, bevor weitere Schäden an ihr verursacht werden können.

- **Angreifer mit administrativer Kontrolle über die Identitätsinfrastruktur einer Umgebung** können dieses Kontrolle verwenden, um Identitäten und Identitätsberechtigungen in dieser Umgebung zu erstellen, zu ändern oder zu löschen.

    Wenn bei einer lokalen Kompromittierung vertrauenswürdige SAML-Token-Signaturzertifikate *nicht* in einem [HSM](../../key-vault/keys/hsm-protected-keys.md) gespeichert sind, umfasst der Angriff den Zugriff auf dieses vertrauenswürdige SAML-Token-Signaturzertifikat.

- **Angreifer können dann das Zertifikat verwenden, um SAML-Token zu fälschen**, um die Identität aller vorhandenen Benutzer und Konten des Unternehmens ohne Zugriff auf Kontoanmeldeinformationen zu benötigen und ohne Spuren zu hinterlassen, zu anzunehmen.

- Der **Zugriff auf Konten mit stark privilegierten Berechtigungen** kann auch verwendet werden, um den vorhandenen Anwendungen Anmeldeinformationen hinzuzufügen, die von Angreifern gesteuert werden, sodass sie unerkannt auf Ihr System zugreifen können, z. B. zum Aufrufen von APIs, indem sie diese Berechtigungen verwenden.

## <a name="responding-to-the-attack"></a>Auf den Angriff reagieren


Das Reagieren auf systemische Identitätskompromittierungen sollte die Schritte umfassen, die in der folgenden Abbildung und Tabelle gezeigt werden:

:::image type="content" source="media/recover-from-identity-compromise/recover-identity-compromise.png" alt-text="Schritte für die Wiederherstellen nach einer Identitätskompromittierung":::.


|Schritt  |BESCHREIBUNG  |
|---------|---------|
|**Einrichten von sicheren Kommunikationsleitungen**     |  Ein Unternehmen, das eine systemische Identitätskompromittierung erfahren hat, muss davon ausgehen, dass die gesamte Kommunikation betroffen ist. Bevor Sie eine Wiederherstellungsaktion ergreifen, müssen Sie sicherstellen, dass die Mitglieder Ihres Teams, die für Ihren Untersuchungs- und Reaktionsaufwand entscheidend sind, [sicher kommunizieren können](#establish-secure-communications). <br><br>*Das Schützen der Kommunikation muss Ihr allererster Schritt sein, damit Sie ohne das Wissen des Angreifers fortfahren können*.|
|**Untersuchen Ihrer Umgebung**   | Nachdem Sie die Kommunikation in Ihrem Kernuntersuchungsteam gesichert haben, können Sie nach anfänglichen Zugriffspunkten und Persistenz-Techniken suchen. [Identifizieren Sie Ihre Anhaltpunkte der Kompromittierung](#identify-indications-of-compromise) und suchen Sie dann nach anfänglichen Zugriffspunkten und Persistenz. Beginnen während Ihrer Wiederherstellungsbemühungen gleichzeitig damit, [kontinuierliche Überwachungsvorgänge einzurichten](#establish-continuous-monitoring).        |
|**Verbessern des Sicherheitsstatus**     | [Aktivieren Sie die Sicherheitsfunktionen und -fähigkeiten](#improve-security-posture) gemäß den Empfehlungen der bewährten Methode für eine verbesserte Systemsicherheit in der Zukunft.  <br><br>Stellen Sie sicher, dass Sie Ihren Aufwand für [kontinuierliche Überwachung](#establish-continuous-monitoring) im Laufe der Zeit fortsetzen und sich die Sicherheitslandschaft ändert.    |
|**Kontrolle wiedererlangen/beibehalten**     |  Sie müssen die administrative Kontrolle über Ihre Umgebung vor dem Angreifer wiedererlangen. Nachdem Sie die Kontrolle wiedererlangt haben und den Sicherheitsstatus Ihres Systems aktualisiert haben, stellen Sie sicher, dass Sie alle möglichen Persistenz-Techniken und neuen Exploits für den anfänglichen Zugriff [korrigieren oder blockieren](#remediate-and-retain-administrative-control).       |
|     |         |

## <a name="establish-secure-communications"></a>Einrichten von sicheren Kommunikationsleitungen

Bevor Sie reagieren, müssen Sie gewährleisten, dass Sie sicher kommunizieren können, ohne dass der Angreifer Sie abhört. Stellen Sie sicher, dass Sie alle Kommunikationen im Zusammenhang mit dem Vorfall isolieren, damit der Angreifer nicht von Ihren Ermittlungen erfährt und von Ihren Reaktionsmaßnahmen überrascht wird.

Beispiel:

1. Für die erste 1:1 – und Gruppenkommunikation können Sie PSTN-Anrufe, Konferenz-Brücken, die nicht mit der Unternehmensinfrastruktur verbunden sind, und End-to-End-verschlüsselte Messaginglösungen verwenden.

    Kommunikation außerhalb dieser Frameworks sollte als kompromittiert und nicht vertrauenswürdig behandelt werden, es sei denn, sie wird über einen sicheren Kanal überprüft.

2. Nach diesen ersten Konversationen sollten Sie einen völlig neuen Microsoft 365-Mandanten erstellen, und zwar isoliert vom Produktions-Mandanten Ihres Unternehmens. Erstellen Sie Konten nur für Mitarbeiter in Schlüsselpositionen, die an der Reaktion beteiligt sein müssen.

Wenn Sie einen neuen Microsoft 365-Mandanten erstellen, achten Sie darauf, alle bewährten Methoden für den Mandanten zu befolgen, insbesondere für Administratorkonten und Rechte. Beschränken Sie Administratorrechte ohne Vertrauensstellungen für externe Anwendungen oder Anbieter.

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie nicht über Ihren neuen Mandanten in Ihren vorhandenen und möglicherweise kompromittierten E-Mail-Konten kommunizieren. 

Weitere Informationen finden Sie unter [Bewährte Methoden für die sichere Verwendung von Microsoft 365](https://www.microsoft.com/security/blog/2019/01/10/best-practices-for-securely-using-microsoft-365-the-cis-microsoft-365-foundations-benchmark-now-available/).

## <a name="identify-indications-of-compromise"></a>Identifizieren von Anzeichen für eine Kompromittierung

Es wird empfohlen, dass die Kunden die Aktualisierungen von Systemanbietern befolgen, einschließlich von Microsoft und allen Partnern. Außerdem wird das Implementieren neuer bereitgestellter Erkennungen und Schutzmaßnahmen und die Identifizierung veröffentlichter Vorfälle der Kompromittierung (IOCs) empfohlen.

Suchen Sie nach Aktualisierungen in den folgenden Microsoft-Sicherheitsprodukten, und implementieren Sie alle empfohlenen Änderungen:

- [Microsoft Sentinel](../../sentinel/index.yml)
- [Microsoft 365 Sicherheitslösungen und -dienste](/microsoft-365/security/)
- [Windows 10 Enterprise Security](/windows/security/)
- [Microsoft Defender für Cloud Apps](/cloud-app-security/)

Die Implementierung neuer Aktualisierungen hilft dabei, alle vorherigen Kampagnen zu identifizieren und zukünftige Kampagnen gegen Ihr System zu verhindern. Beachten Sie, dass Listen mit IOCs möglicherweise nicht vollständig sind und sich mit der Fortsetzung der Untersuchungen erweitern.

Daher wird empfohlen, auch die folgenden Aktionen durchzuführen:

- Stellen Sie sicher, dass Sie die [Azure-Sicherheits-Benchmark-Dokumentation](/security/benchmark/azure/) angewendet haben und die Einhaltung der Richtlinien über [Microsoft Defender für Cloud](../../security-center/index.yml) überwachen.

- Integrieren Sie Bedrohungsdaten-Feeds in Ihr SIEM, z. B. durch die Konfiguration von Microsoft 365-Datenkonnektoren in [Microsoft Sentinel](../../sentinel/understand-threat-intelligence.md).

Weitere Informationen finden Sie in der Sicherheitsdokumentation von Microsoft:

- [Microsoft-Dokumentation zur Sicherheit](/security/)
- [Dokumentation zur Azure-Sicherheit](../index.yml)

## <a name="investigate-your-environment"></a>Untersuchen Ihrer Umgebung

Sobald Ihr Vorfall-Antwortdienst und Ihre Schlüsselmitarbeiter einen sicheren Ort für die Zusammenarbeit haben, können Sie mit der Untersuchung der kompromittierten Umgebung beginnen.

Sie müssen ein Gleichgewicht zwischen jedem anomalen Verhalten auf den Grund gehen und schnellen Maßnahmen ergreifen finden, um weitere Aktivitäten des Angreifers zu beenden. Jede erfolgreiche Bereinigung erfordert ein Verstehen der anfänglichen Methode der Eingabe- und Persistenz-Methoden, die der Angreifer verwendet hat, so vollständig wie möglich zu diesem Zeitpunkt. Alle Persistenz-Methoden, die während der Untersuchung übersehen werden, können zu einem fortgesetzten Zugriff durch den Angreifer und einer potenziellen erneuten Kompromittierung führen.

An diesem Punkt sollten Sie eine Risikoanalyse durchführen, um Ihre Aktionen zu priorisieren. Weitere Informationen finden Sie unter:

- [Bedrohung, Sicherheitsrisiko und Risikobewertung für Rechenzentren](/compliance/assurance/assurance-threat-vulnerability-risk-assessment)
- [Nachverfolgen und Reagieren auf neue Bedrohungen mit Bedrohungsanalysen](/microsoft-365/security/defender-endpoint/threat-analytics)
- [Bedrohungs- und Sicherheitsrisikomanagement](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt)

Die Sicherheitsdienste von Microsoft stellen umfangreiche Ressourcen für detaillierte Untersuchungen zur Verfügung. In den folgenden Abschnitten werden die wichtigsten empfohlenen Aktionen beschrieben.


> [!NOTE]
> Wenn Sie feststellen, dass eine oder mehrere der aufgelisteten Protokollierungsquellen derzeit nicht Teil Ihres Sicherheitsprogramms sind, empfehlen wir Ihnen, sie so bald wie möglich zu konfigurieren, um Erkennungen und zukünftige Protokollüberprüfungen zu aktivieren.
>
> Stellen Sie sicher, dass Sie Ihre Protokollaufbewahrung so konfigurieren, dass die Untersuchungsziele Ihrer Organisation in Zukunft unterstützt werden. Bewahren Sie den Beweis nach Bedarf für rechtliche, behördliche oder versicherungstechnische Zwecke auf.
>

### <a name="investigate-and-review-cloud-environment-logs"></a>Untersuchen und Überprüfen Sie Ihre Cloudumgebungsprotokolle

Untersuchen und überprüfen Sie Ihre Cloudumgebungsprotokolle auf verdächtige Aktionen und Anzeichen einer Kompromittierung durch Angreifer. Überprüfen Sie beispielsweise die folgenden Protokolle:

- [Einheitliche Überwachungsprotokolle (Unified Audit Logs, UAL)](/powershell/module/exchange/search-unifiedauditlog)
- [Protokolle von Azure Active Directory (Azure AD)](../../active-directory/reports-monitoring/overview-monitoring.md)
- [Lokale Protokolle von Microsoft Exchange ](/exchange/mail-flow/transport-logs/transport-logs)
- VPN-Protokolle, z. B. vom [VPN Gateway](../../vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)
- Technische Systemprotokolle
- Protokolle zur Antiviren- und Endpunkterkennung

### <a name="review-endpoint-audit-logs"></a>Überprüfen von Endpunktüberwachungsprotokollen

Überprüfen Sie Ihre Endpunktüberwachungsprotokolle auf lokale Änderungen, z. B. auf die folgenden Arten von Aktionen:

- Änderungen an der Gruppenmitgliedschaft
- Erstellung von neuen Benutzerkonten
- Delegationen innerhalb von Active Directory

Berücksichtigen Sie insbesondere alle diese Änderungen, die zusammen mit anderen typischen Anzeichen einer Kompromittierung oder Aktivität auftreten.

### <a name="review-administrative-rights-in-your-environments"></a>Überprüfen Sie die Administratorrechte in Ihren Umgebungen

Überprüfen Sie die Administratorrechte sowohl in Ihrer Cloudumgebung als auch in ihrer lokalen Umgebung. Beispiel:

|Environment  |BESCHREIBUNG  |
|---------|---------|
|**Alle Cloudumgebungen**    |       – Überprüfen Sie alle privilegierten Zugriffsrechte in der Cloud und entfernen Sie alle unnötigen Berechtigungen<br>    – Implementieren Sie Azure AD Privileged Identity Management (PIM)<br>    – Richten Sie Richtlinien für bedingten Zugriff, um den Administratorzugriff während der Härtung zu beschränken, ein      |
|**Alle lokalen Umgebungen**     |       – Überprüfen Sie den lokalen privilegierten Zugriff und Entfernen Sie unnötige Berechtigungen<br>   – Reduzieren Sie die Mitgliedschaft in den integrierten Gruppen<br>    – Überprüfen Sie die Active Directory-Delegationen<br>    – Härten Sie Ihre Ebene 0-Umgebung und beschränken Sie, wer Zugriff auf die Objekte der Ebene-0 hat      |
|**Alle Unternehmensanwendungen**     | Überprüfen Sie, ob delegierte Berechtigungen und Zustimmungsberechtigungen erteilt wurden, die eine der folgenden Aktionen zulassen: <br><br>  – Ändern von privilegierten Benutzern und Rollen <br>– Lesen oder Zugreifen auf alle Postfächer <br>– Senden oder Weiterleiten von E-Mails im Namen anderer Benutzer <br>– Zugreifen auf alle OneDrive oder SharePoint Websiteinhalte <br>– Hinzufügen von Dienstprinzipals, die Lese-/Schreibzugriff auf das Verzeichnis haben      |
|**Microsoft 365-Umgebungen**     |Überprüfen Sie die Zugriffs- und Konfigurationseinstellungen für Ihre Microsoft 365 Umgebung, einschließlich: <br>– SharePoint Online-Freigaben <br>– Microsoft Teams <br>– Power Apps <br>– Microsoft OneDrive for Business          |
| **Überprüfen von Benutzerkonten in Ihren Umgebungen**   |– Überprüfen und entfernen Sie Gastbenutzerkonten, die nicht mehr benötigt werden. <br>– Überprüfen Sie die E-Mail-Konfigurationen für Delegaten, Postfachordnerberechtigungen, Registrierungen mobiler ActiveSync-Geräte, Posteingangsregeln und Outlook Weboptionen. <br>– Überprüfen Sie die ApplicationImpersonation-Rechte, und reduzieren Sie die Verwendung der Legacyauthentifizierung so weit wie möglich. <br>– Überprüfen Sie, ob MFA erzwungen wird und ob die Kontaktinformationen für MFA und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) für alle Benutzer korrekt sind.         |
|     |         |

## <a name="establish-continuous-monitoring"></a>Einrichten der kontinuierlichen Überwachung

Die Erkennung des Angreiferverhaltens umfasst mehrere Methoden und hängt von den Sicherheitstools ab, die Ihrem Unternehmen für die Reaktion auf den Angriff zur Verfügung stehen.

Beispielsweise können Microsoft-Sicherheitsdienste über bestimmte Ressourcen und Anleitungen verfügen, die für den Angriff relevant sind. Die sind in den folgenden Abschnitten beschrieben.

> [!IMPORTANT]
> Wenn Ihre Untersuchung Beweise für Administratorberechtigungen findet, die durch die Kompromittierung auf Ihrem System erworben wurden, die Zugriff auf das globale Administratorkonto und/oder das vertrauenswürdige SAML-Token-Signaturzertifikat Ihres Unternehmens gewährt haben, empfehlen wir Ihnen, Maßnahmen zu ergreifen, um die [administrative Kontrolle zu wiederherzustellen und zu behalten](#remediate-and-retain-administrative-control).
>

### <a name="monitoring-with-microsoft-sentinel"></a>Überwachen mit Microsoft Sentinel

Microsoft Sentinel verfügt über viele integrierte Ressourcen, die Sie bei Ihrer Untersuchung unterstützen. Diese beinhalten z. B. Hunting-Arbeitsmappen und Analyseregeln, die dabei helfen können, die Angriffe in relevanten Bereichen Ihrer Umgebung zu erkennen.

Weitere Informationen finden Sie unter:

- [Visualisieren und Analysieren Ihrer Umgebung](../../sentinel/get-visibility.md)
- [Standardmäßig verfügbare Erkennung von Bedrohungen](../../sentinel/detect-threats-built-in.md).

### <a name="monitoring-with-microsoft-365-defender"></a>Überprüfungen mit dem Microsoft 365 Defender durchführen

Es wird empfohlen, dass Sie den Microsoft 365 Defender for Endpoint und den Microsoft Defender Antivirus für spezifische Anleitungen für Ihren Angriff einbeziehen.

Weitere Beispiele für Erkennungen, Suchabfragen und Bedrohungsanalyseberichte finden Sie im Microsoft Security Center, z. B. in Microsoft 365 Defender, Microsoft 365 Defender für Identität und Microsoft Defender für Cloud-Apps. Um die Abdeckung zu gewährleisten, stellen Sie sicher, dass Sie den [Microsoft Defender for Identity-Agent](/defender-for-identity/install-step4) zusätzlich zu allen Domänencontrollern auf ADFS-Servern installieren.

Weitere Informationen finden Sie unter:

- [Nachverfolgen und Reagieren auf neue Bedrohungen mit Bedrohungsanalysen](/windows/security/threat-protection/microsoft-defender-atp/threat-analytics)
- [Verstehen des Analyseberichts in den Bedrohungsanalysen](/microsoft-365/security/defender/threat-analytics-analyst-reports)

### <a name="monitoring-with-azure-active-directory"></a>Überprüfen mit Azure Active Directory

Azure Active Directory Anmeldeprotokolle können anzeigen, ob die mehrstufige Authentifizierung ordnungsgemäß verwendet wird. Greifen Sie direkt über den Azure Active Directory-Bereich im Azure-Portal auf die Anmeldeprotokolle zu, verwenden Sie das Cmdlet **Get-AzureADAuditSignInLogs** oder zeigen Sie diese im Bereich **Protokolle** von Microsoft Sentinel an.

Suchen oder filtern Sie zum Beispiel die Ergebnisse, wenn das Feld **MFA-Ergebnis** den Wert von der **MFA-Anforderung durch Anspruch im Token erfüllt**. Wenn Ihr Unternehmen AD FS verwendet und die protokollierten Ansprüche nicht in der ADFS-Konfiguration enthalten sind, können diese Ansprüche auf Angreiferaktivitäten hinweisen.

Suchen oder filtern Sie Ihre Ergebnisse weiter, um zusätzliches Fehler auszuschließen. Sie können z. B. nur Ergebnisse aus den Verbunddomänen einbeziehen. Wenn Sie verdächtige Anmeldungen finden, führen Sie basierend auf IP-Adressen, Benutzerkonten und so weiter einen Drilldown aus.

In der folgenden Tabelle werden weitere Methoden für die Verwendung von Azure Active Directory-Protokollen für Ihre Untersuchung beschrieben:

|Methode  |BESCHREIBUNG  |
|---------|---------|
|**Analysieren von riskanten Anmeldeereignisse**     |  Azure Active Directory und ihre Identity Protection-Plattform können Risikoereignisse im Zusammenhang mit der Verwendung von den vom Angreifer generierten SAML-Token generieren. <br><br>Diese Ereignisse könnten als *unbekannte Eigenschaften*, *anonyme IP-Adresse*, *unmöglicher Ortswechsel* und so weiter, bezeichnet werden. <br><br>Es wird empfohlen, alle Risikoereignisse im Zusammenhang mit den Konten mit Administratorrechten genau zu analysieren, einschließlich aller Konten, die möglicherweise automatisch verworfen oder bereinigt wurden. Beispielsweise kann ein Risikoereignis oder eine anonyme IP-Adresse automatisch bereinigt werden, da der Benutzer die MFA bestanden hat. <br><br>Stellen Sie sicher, dass Sie [ADFS Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md) verwenden, damit alle Authentifizierungsereignisse in Azure AD sichtbar sind. |
|**Erkennen von Eigenschaften der Domänenauthentifizierung**     |  Jeder Versuch des Angreifers, die Richtlinien der Domänenauthentifizierung zu bearbeiten, wird in den Azure Active Directory-Überwachungsprotokollen aufgezeichnet und im einheitlichen Überwachungsprotokoll widergespiegelt. <br><br> Überprüfen Sie beispielsweise alle Ereignisse im Zusammenhang mit **Domänenauthentifizierung festlegen** im einheitlichen Überwachungsprotokoll, in Azure AD-Überwachungsprotokollen und/oder in Ihrer SIEM-Umgebung, um sicherzustellen, dass alle aufgeführten Aktivitäten erwartet und geplant wurden.   |
|**Erkennen von Anmeldeinformationen für die OAuth-Anwendungen**     |  Angreifer, die die Kontrolle über ein privilegiertes Konto erlangt haben, können nach einer Anwendung suchen, die auf die E-Mails eines Benutzers in dem Unternehmen zugreifen kann. Mit dieser Anwendung können dann von Angreifern kontrollierte Anmeldeinformationen hinzufügt werden. <br><br>Beispielsweise können Sie nach einer der folgenden Aktivitäten suchen, die mit dem Verhalten des Angreifers konsistent wäre: <br>– Hinzufügen oder Aktualisieren von Dienstprinzipal-Anmeldeinformationen <br>– Aktualisieren von Anwendungszertifikaten und Geheimnissen <br>– Hinzufügen einer Zuweisung von App-Rollenzuweisungen zu einem Benutzer <br>– Hinzufügen von Oauth2PermissionGrant |
|**Erkennen des E-Mail-Zugriffs durch Anwendungen**     |  Suchen Sie nach dem Zugriff auf E-Mails durch Anwendungen in Ihrer Umgebung. Verwenden Sie zum Beispiel die [erweiterten Auditing-Funktionen von Microsoft 365](/microsoft-365/compliance/mailitemsaccessed-forensics-investigations), um kompromittierte Konten zu untersuchen. |
|**Erkennen von nicht interaktiven Anmeldungen bei Dienstprinzipals**     | Die Azure Active Directory-Anmeldeberichte enthalten Details zu nicht interaktiven Anmeldungen, bei denen die Anmeldeinformationen für den Dienstprinzipal verwendet wurden.  Beispielsweise können Sie die Anmeldeberichte verwenden, um wertvolle Daten für Ihre Untersuchung zu finden, z. B. eine IP-Adresse, die vom Angreifer für den Zugriff auf die E-Mail-Anwendungen verwendet wird.        |
|     |         |


## <a name="improve-security-posture"></a>Verbessern des Sicherheitsstatus

Wenn in Ihren Systemen ein Sicherheitsereignis aufgetreten ist, empfiehlt es sich, ihre aktuelle Sicherheitsstrategie und Prioritäten zu reflektieren.

Die Vorfall Antwortdienste werden häufig aufgefordert, Empfehlungen dazu zu geben, welche Investitionen das Unternehmen priorisieren sollte, da sie nun mit neuen Bedrohungen konfrontiert ist.

Zusätzlich zu den Empfehlungen, die in diesem Artikel dokumentiert sind, empfiehlt es sich, die Schwerpunktbereiche zu priorisieren, die auf die von diesem Angreifer verwendeten Techniken nach der Ausnutzung reagieren und die allgemeinen Sicherheitsstatuslücken zu priorisieren, die diese ermöglichen.

In den folgenden Abschnitten werden Empfehlungen zur Verbesserung des allgemeinen Sicherheitsstatus und des Identitätssicherheitsstatus aufgeführt.

### <a name="improve-general-security-posture"></a>Verbessern des allgemeinen Sicherheitsstatus

Wir empfehlen die folgenden Aktionen, um Ihren allgemeinen Sicherheitsstatus sicherzustellen:

- **Überprüfen Sie [Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score)** auf Empfehlungen zu den Sicherheitsvorkehrungen, die für die von Ihnen verbrauchten Microsoft-Produkte und -Dienste angepasst wurden.

- **Stellen Sie sicher, dass Ihr Unternehmen über EDR- und SIEM-Lösungen**, wie z. B. den [Microsoft 365 Defender for Endpoint](/microsoft-365/security/defender/microsoft-365-defender) und [Microsoft Sentinel](../../sentinel/overview.md) verfügt.

- **Überprüfen Sie das Microsoft [Enterprise-Zugriffsmodell](/security/compass/privileged-access-access-model)** .

### <a name="improve-identity-security-posture"></a>Verbessern des Identitätssicherheitsstatus

Wir empfehlen die folgenden Aktionen, um Ihren Identitätsbezogenen Sicherheitsstatus sicherzustellen:

- **Überprüfen Sie die [Fünf Schritte zum Schützen Ihrer Identitätsinfrastruktur](steps-secure-identity.md)** von Microsoft und priorisieren Sie die Schritte entsprechend Ihrer Identitätsarchitektur.

- **[Erwägen Sie die Migration zu Azure AD Sicherheitsstandards](../../active-directory/fundamentals/concept-fundamentals-security-defaults.md)** für Ihre Authentifizierungsrichtlinie.

- **Beseitigen Sie die Verwendung der Legacyauthentifizierung Ihres Unternehmens**, wenn die Systeme oder Anwendungen diese weiterhin benötigen. Weitere Informationen finden Sie unter [Blockieren der Legacyauthentifizierung bei Azure AD mit bedingtem Zugriff](../../active-directory/conditional-access/block-legacy-authentication.md).

    > [!NOTE]
    > Das Exchange Team plant, die [Standardauthentifizierung für die Protokolle EAS, EWS, POP, IMAP und RPS](https://developer.microsoft.com/en-us/office/blogs/deferred-end-of-support-date-for-basic-authentication-in-exchange-online/) im zweiten Halbjahr 2021 zu deaktivieren.
    >
    > Sie sind aus Gründen der Übersichtlichkeit, Sicherheit und Authentifizierungsrichtlinien voneinander getrennt, bieten jedoch ergänzende Funktionen.
    >
    > Es wird empfohlen, dass Kunden Authentifizierungsrichtlinien verwenden, um die Standardauthentifizierung für eine Teilmenge von Exchange Online-Protokollen zu deaktivieren. Alternative können sie die Standardauthentifizierung in einem großen Unternehmen schrittweise zu deaktivieren.
    >

- **Behandeln Sie Ihre ADFS-Infrastruktur und die AD Connect-Infrastruktur als ein Objekt der Ebene-0**.

- **Schränken Sie den lokalen Administratorzugriff auf das System** ein, einschließlich des Kontos, das zum Ausführen des ADFS-Diensts verwendet wird.

    Die geringste Berechtigung, die für das Konto, auf dem ADFS läuft, erforderlich ist, ist die Zuweisung für Benutzerrechte *Als Dienst anmelden*.

- **Beschränken Sie den Administratorzugriff auf eingeschränkte Benutzer und aus eingeschränkten IP-Adressbereichen**, indem Sie die Windows Firewall-Richtlinien für einen Remotedesktop verwenden.

    Es wird empfohlen, eine Jumpbox der Ebene 0 oder ein entsprechendes System einzurichten.

- **Blockieren Sie sämtlichen eingehenden SMB-Zugriff** auf die Systeme von überall aus der Umgebung. Weitere Informationen finden Sie unter [Beyond the Edge: So sichern Sie den SMB-Datenverkehr in Windows](https://techcommunity.microsoft.com/t5/itops-talk-blog/beyond-the-edge-how-to-secure-smb-traffic-in-windows/ba-p/1447159). Außerdem wird empfohlen, die Windows Firewall-Protokolle zur Verlaufsüberwachung und proaktiven Überwachung an SIEM zu streamen.

- Wenn Sie ein Dienstkonto verwenden und von Ihrer Umgebung unterstützt werden, **migrieren Sie von einem Dienstkonto zu einem gruppenverwalteten Dienstkonto (gMSA)** . Wenn Sie nicht zu einem gMSA wechseln können, rotieren Sie das Kennwort für das Dienstkonto in ein komplexes Kennwort.

- **Stellen Sie sicher, dass die ausführliche Protokollierung auf Ihren ADFS-Systemen aktiviert ist**. Führen Sie beispielsweise die folgenden Befehle aus:

    ```powershell
    Set-AdfsProperties -AuditLevel verbose
    Restart-Service -Name adfssrv
    Auditpol.exe /set /subcategory:”Application Generated” /failure:enable /success:enable
    ```

## <a name="remediate-and-retain-administrative-control"></a>Korrigieren und Beibehalten der administrativen Kontrolle

Wenn Ihre Untersuchung ergeben hat, dass der Angreifer die administrative Kontrolle über die Cloud- oder lokale Umgebung des Unternehmens hat, müssen Sie die Kontrolle auf eine Weise wiedererlangen, die sicherstellt, dass der Angreifer nicht persistent ist.

Dieser Abschnitt enthält mögliche Methoden und Schritte, die beim Erstellen Ihres Wiederherstellungsplans für die Administrative Steuerung zu berücksichtigen sind.

> [!IMPORTANT]
> Die genauen Schritte, die in Ihrem Unternehmen erforderlich sind, hängen davon ab, welche Persistenz Sie in Ihrer Untersuchung ermittelt haben und wie sicher Sie sind, dass Ihre Untersuchung abgeschlossen wurde und alle möglichen Eingabe- und Persistenzmethoden ermittelt hat.
>
> Stellen Sie sicher, dass alle ausgeführten Aktionen von einem vertrauenswürdigen Gerät ausgeführt werden, das aus einer [bereinigten Quelle](/security/compass/privileged-access-access-model) erstellt wurde. Verwenden Sie zum Beispiel eine neue [Arbeitsstation mit privilegiertem Zugang](/security/compass/privileged-access-deployment).
>

Die folgenden Abschnitte enthalten die folgenden Arten von Empfehlungen zur Wartung und Beibehaltung der administrativen Kontrolle:

- Entfernen der Vertrauensstellung von Ihren aktuellen Servern
- Rotieren Ihres SAML-Token-Signaturzertifikats oder Ersetzen Ihrer ADFS-Servers, falls erforderlich
- Spezifische Wartungsaktivitäten für Cloudumgebungen oder lokale Umgebungen

### <a name="remove-trust-on-your-current-servers"></a>Entfernen Sie die Vertrauensstellung von Ihren aktuellen Servern

Wenn Ihr Unternehmen die Kontrolle über die Token-Signaturzertifikate oder die Verbundvertrauensstellung verloren hat, besteht der sicherste Ansatz darin, die Vertrauensstellung zu entfernen und zur der von Cloud beherrschten Identität zu wechseln, während Sie die lokale Vertrauenswürdigkeit beheben.

Das Entfernen von Vertrauensstellungen und das Wechseln zu einer von Cloud beherrschten Identität erfordert eine sorgfältige Planung. Außerdem ist ein tiefgreifendes Verstehen der Auswirkungen der Isolation der Identität auf den Geschäftsbetrieb erforderlich. Weitere Informationen hierzu, finden Sie unter [Schützen von Microsoft 365 vor Angriffen auf die lokale Umgebung](../../active-directory/fundamentals/protect-m365-from-on-premises-attacks.md).

### <a name="rotate-your-saml-token-signing-certificate"></a>Rotieren Ihres SAML-Token-Signaturzertifikats

Wenn Ihre Organisation entscheidet, die [Vertrauensstellung](#remove-trust-on-your-current-servers) *nicht* während der Wiederherstellen der administrativen lokalen Kontrolle aufzuheben, müssen Sie Ihr SAML-Token-Signaturzertifikat rotieren, nachdem Sie die lokale administrative Kontrolle wiedererlangt haben und die Angreifer daran gehindert haben, erneut auf das Signaturzertifikat zuzugreifen.

Durch das einmalige Rotieren des Token-Signaturzertifikats kann das vorherige Token-Signaturzertifikat weiterhin funktionieren. Die Aufrechterhaltung der Funktionsfähigkeit früherer Zertifikate ist eine integrierte Funktion für normale Zertifikatsrotationen. Sie räumt den Unternehmen eine Frist ein, um alle Vertrauensstellungen zu aktualisieren, bevor das Zertifikat abläuft.

Im Falle eines Angriffs, soll der Angreifer überhaupt keinen Zugriff behalten. Stellen Sie mit den folgenden Schritten sicher, dass der Angreifer nicht die Fähigkeit erlangt, Token für Ihre Domain zu fälschen.

> [!CAUTION]
> Der letzte Schritt in diesem Verfahren protokolliert die Benutzer von ihren Smartphones, aktuellen Webmailsitzungen und allen anderen Elementen, die die zugeordneten Token und Aktualisierungstoken verwenden.
>

> [!TIP]
> Wenn Sie diese Schritte in Ihrer ADFS-Umgebung ausführen, wird sowohl ein primäres als auch ein sekundäres Zertifikat erstellt. Das sekundäre Zertifikat wird nach einem Standardzeitraum von fünf Tagen automatisch auf das primäre Zertifikat heraufgestuft.
>
> Wenn Sie über Vertrauensstellungen der vertrauenden Seite verfügen, kann dies fünf Tage nach der anfänglichen Änderung der ADFS-Umgebung Auswirkungen haben und sollte in Ihrem Plan berücksichtigt werden. Sie können dies auch beheben, indem Sie das primäre Zertifikat ein drittes Mal ersetzen. Dafür verwenden Sie die Kennzeichnung **Dringend** erneut und entfernen das sekundäre Zertifikat oder deaktivieren die automatische Zertifikatrotation.
>

**So rotieren Sie das Token-Signaturzertifikat vollständig und verhindern, dass ein Angreifer neue Token fälscht**

1. Vergewissern Sie sich, dass die Option **AutoCertificateRollover** auf **True** festgelegt ist:

    ``` powershell
    Get-AdfsProperties | FL AutoCert*, Certificate*
    ```
    Wenn **AutoCertificateRollover** nicht auf **True** gesetzt ist, legen Sie den Wert wie folgt fest:

    ``` powershell
    Set-ADFSProperties -AutoCertificateRollover $true
    ```

1. Stellen Sie eine Verbindung zum Microsoft-Onlinedienst her:

    ``` powershell
    Connect-MsolService
    ```

1. Führen Sie den folgenden Befehl aus, und notieren Sie sich den Fingerabdruck und das Ablaufdatum des lokalen Tokens und des Cloudtoken-Signaturzertifikats:

    ``` powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

    Beispiel:

    ```powershell
    ...
    [Not Before]
        12/9/2020 7:57:13 PM

    [Not After]
        12/9/2021 7:57:13 PM

    [Thumbprint]
        3UD1JG5MEFHSBW7HEPF6D98EI8AHNTY22XPQWJFK6
    ```

1. Ersetzen Sie das primäre Token-Signaturzertifikat unter Verwendung des Schalters **Dringend**. Dieser Befehl bewirkt, dass das ADFS das primäre Zertifikat sofort ersetzt, ohne es zu einem sekundären Zertifikat zu machen:

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing -Urgent
    ```

1. Erstellen Sie ein sekundäres Token-Signaturzertifikat unter ohne den Schalter **Dringend** zu verwenden. Dieser Befehl ermöglicht zwei lokale Token-Signaturzertifikate vor der Synchronisierung mit Azure Cloud.

    ```powershell
    Update-AdfsCertificate -CertificateType Token-Signing
    ```

1. Aktualisieren Sie die Cloudumgebung mit den primären und sekundären Zertifikaten lokal, um das in der Cloud veröffentlichte Token-Signaturzertifikat sofort zu entfernen.

    ```powershell
    Update-MsolFederatedDomain -DomainName <domain>
    ```

    > [!IMPORTANT]
    > Wenn dieser Schritt nicht mit dieser Methode ausgeführt wird, kann das alte Token-Signaturzertifikat möglicherweise weiterhin Benutzer authentifizieren.

1. Um sicherzustellen, dass diese Schritte ordnungsgemäß ausgeführt wurden, überprüfen Sie, ob das zuvor in Schritt 3 angezeigte Zertifikat jetzt entfernt wurde:

    ```powershell
    Get-MsolFederationProperty -DomainName <domain>
    ```

1. Widerrufen Sie Ihre Aktualisierungstoken über PowerShell, um den Zugriff mit den alten Token zu verhindern. 

    Weitere Informationen finden Sie unter:

    - [Widerrufen des Benutzerzugriffs in Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Revoke-AzureADUserAllRefreshToken PowerShell Docs](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)



### <a name="replace-your-adfs-servers"></a>Ersetzen Ihrer ADFS-Server

Wenn Sie sich entscheiden, die ADFS-Server durch bereinigte Systeme zu ersetzen, anstatt [Ihr SAML-Token-Signaturzertifikat zu rotieren](#rotate-your-saml-token-signing-certificate) müssen Sie das vorhandene AD FS aus Ihrer Umgebung entfernen und dann ein neues erstellen. 

Weitere Informationen finden Sie unter [Entfernen eine Konfiguration](../../active-directory/cloud-sync/how-to-configure.md#remove-a-configuration). 

### <a name="cloud-remediation-activities"></a>Cloud-Wartungsaktivitäten

Zusätzlich zu den weiter oben in diesem Artikel aufgeführten Empfehlungen empfehlen wir auch die folgenden Aktivitäten für Ihre Cloudumgebungen:

|Aktivität  |BESCHREIBUNG  |
|---------|---------|
|**Zurücksetzen von Kennwörtern**     |   Setzen Sie Kennwörter für alle [Break-Glass-Konten](../../active-directory/roles/security-emergency-access.md) zurück und reduzieren Sie die Anzahl der Break-Glass-Konten für die Sicherheit auf das absolute erforderliche Minimum.    |
|**Beschränken von Konten mit privilegiertem Zugriff**     |    Stellen Sie sicher, dass die Dienst- und Benutzerkonten mit privilegiertem Zugriff ausschließlich cloudbasierte Konten sind und keine lokalen Konten verwenden, die mit Azure Active Directory synchronisiert oder verbunden sind.  |
|**MFA erzwingen**     | Erzwingen Sie die Multi-Factor Authentifizierung (MFA) für alle Benutzer mit erhöhten Rechten in dem Mandanten. Es wird empfohlen, MFA für alle Benutzer in dem Mandanten zu erzwingen.       |
|**Begrenzen der Administratorrechte**     |    Implementieren von [Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) (PIM) und bedingtem Zugriff, um administrativen Zugriff zu begrenzen.  <br><br>Implementieren Sie für Microsoft 365 Benutzer [Privileged Access Management](https://techcommunity.microsoft.com/t5/microsoft-security-and/privileged-access-management-in-office-365-is-now-generally/ba-p/261751) (PAM), um den Zugriff auf sensible Funktionen wie eDiscovery, globaler Administrator, Kontoverwaltung und vieles mehr zu beschränken.    |
|**Überprüfen/Reduzieren von delegierten Berechtigungen und Zustimmungszuweisungen**     |  Überprüfen und reduzieren Sie alle delegierten Berechtigungen oder [Zustimmungszuweisungen](/graph/auth-limit-mailbox-access) für Enterprise Anwendungen, die eine der folgenden Funktionen zulassen: <br><br>– Änderung von privilegierten Benutzern und Rollen <br>– Lesen, Senden von E-Mails oder Zugreifen auf alle Postfächer <br>– Zugreifen auf OneDrive, Teams oder SharePoint Inhalte <br>– Hinzufügen von Dienstprinzipals, die Lese-/Schreibzugriff auf das Verzeichnis haben <br>– Anwendungsberechtigungen im Vergleich zu delegiertem Zugriff       |
|     |         |

### <a name="on-premises-remediation-activities"></a>Lokale Wartungsaktivitäten

Zusätzlich zu den weiter oben in diesem Artikel aufgeführten Empfehlungen empfehlen wir auch die folgenden Aktivitäten für Ihre Cloudumgebungen:

|Aktivität  |BESCHREIBUNG  |
|---------|---------|
|**Neuerstellen betroffener Systeme**     |   Erstellen Sie die Systeme neu, die während ihrer Untersuchung vom Angreifer als kompromittiert identifiziert wurden.      |
|**Entfernen unnötiger Administratorbenutzer**     |   Entfernen Sie unnötige Mitglieder aus den Domänenadministratoren, Sicherungsoperatoren und Enterprise Administratorgruppen. Weitere Informationen finden Sie unter [Sichern des privilegierten Zugriffs](/security/compass/overview). |
|**Zurücksetzen von Kennwörtern auf privilegierte Konten**     |  Setzen Sie Kennwörter aller privilegierten Konten in der Umgebung zurück. <br><br>**Hinweis**: Privilegierte Konten sind nicht auf integrierte Gruppen beschränkt. Sie können auch Gruppen sein, denen der Zugriff auf die Serververwaltung, die Arbeitsstationsverwaltung oder andere Bereiche Ihrer Umgebung übertragen wurde.      |
|**Zurücksetzen des krbtgt-Kontos**     | Setzen Sie das **krbtgt**-Konto zweimal mit dem Skript [New-KrbtgtKeys](https://github.com/microsoft/New-KrbtgtKeys.ps1/blob/master/New-KrbtgtKeys.ps1) zurück. <br><br>**Hinweis**: Wenn Sie schreibgeschützte Domänencontroller verwenden, müssen Sie das Skript separat für Domänencontroller mit Lese-/Schreibzugriff und für schreibgeschützte Domänencontroller ausführen.        |
|**Planen eines Systemneustarts**     |   Nachdem Sie überprüft haben, ob keine vom Angreifer erstellten Persistenz-Mechanismen vorhanden sind oder auf Ihrem System verbleiben, planen Sie einen Systemneustart, um das Entfernen von speicherresidenter Schadsoftware zu unterstützen. |
|**Zurücksetzen des DSRM-Kennworts**     |  Setzen Sie das DSRM-Kennwort (Verzeichnisdienste-Wiederherstellungsmodus) jedes Domänencontrollers auf ein eindeutiges und komplexes Kennwort zurück.       |
|     |         |

### <a name="remediate-or-block-persistence-discovered-during-investigation"></a>Beheben oder Blockieren Sie die Persistenz, die während der Untersuchung ermittelt wurde

Die Untersuchung ist ein iterativer Prozess. Sie müssen abwägen zwischen dem Wunsch des Unternehmens, bei der Identifizierung von Anomalien Abhilfe zu schaffen, und dem Risiko, dass der Angreifer durch die Abhilfemaßnahmen auf Ihre Entdeckung aufmerksam wird und Zeit hat, zu reagieren.

Beispielsweise kann ein Angreifer, der die Erkennung wahrnimmt, Techniken ändern oder mehr Persistenz erstellen.

Stellen Sie sicher, dass Sie alle Persistenz-Techniken beheben, die Sie in früheren Phasen der Untersuchung identifiziert haben.

### <a name="remediate-user-and-service-account-access"></a>Beheben des Benutzer- und Dienstkontozugriffs

Zusätzlich zu den oben aufgeführten empfohlenen Aktionen empfiehlt es sich, die folgenden Schritte zum Bereinigen und Wiederherstellen von Benutzerkonten zu berücksichtigen:

- **Erzwingen Sie den bedingten Zugriff basierend auf den vertrauenswürdigen Geräten**. Wenn möglich, empfehlen wir Ihnen, eine *standortabhängige Zugangskontrolle* einzuführen, die Ihren Unternehmensanforderungen entspricht.

- **Setzen Sie die Passwörter** nach der Entfernung für alle Benutzerkonten zurück, die möglicherweise kompromittiert wurden. Stellen Sie außerdem sicher, dass Sie einen kurzfristigen Plan zum Zurücksetzen von Anmeldeinformationen für alle Konten in Ihrem Verzeichnis implementieren.

- **Widerrufen Sie Aktualisierungstoken** sofort nach dem Rotieren Ihrer Anmeldeinformationen.

    Weitere Informationen finden Sie unter:

    - [Widerrufen des Benutzerzugriffs in Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md)
    - [Wiederrufen der „AzureADUserAllRefreshToken PowerShell“ Dokumentation](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)

## <a name="next-steps"></a>Nächste Schritte

- **Abrufen von Hilfe innerhalb der Microsoft-Produkte**, einschließlich des Microsoft 365 Defender-Portals, des Microsoft 365 Security & Compliance Center und des Office 365 Security Center, indem Sie die Schaltfläche **Hilfe** ( **?** ) in der oberen Navigationsleiste auswählen.

- Wenn Sie **Unterstützung bei der Bereitstellung** benötigen, wenden Sie sich an [FastTrack](https://fasttrack.microsoft.com) Support

- **Wenn Sie Anforderungen an den Produktsupport haben**, können Sie eine [Microsoft-Support-Case-Datei](https://support.microsoft.com/contactus) erstellen.

    > [!IMPORTANT]
    > Wenn Sie der Meinung sind, dass Sie kompromittiert wurden und Unterstützung durch eine Reaktion auf Vorfälle benötigen, öffnen Sie eine **Sev A** Microsoft-Support-Anfrage.
    >
