---
title: Microsoft Defender für Server – Vorteile und Features
description: Lernen Sie die Vorteile und Features von Microsoft Defender für Server kennen.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 121809ab32db6811eb6755c857f13a3469cbcfa0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131061046"
---
# <a name="introduction-to-microsoft-defender-for-servers"></a>Einführung in Microsoft Defender für Server

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Server stattet Ihre Windows- und Linux-Computer mit Bedrohungserkennung und erweiterten Schutzmaßnahmen aus.

Unter Windows wird Microsoft Defender für Cloud zur Überwachung und zum Schutz Ihrer Windows-basierten Computer in Azure-Dienste integriert. Defender für Cloud stellt Warnungen und Behebungsvorschläge aus allen diesen Diensten in einem benutzerfreundlichen Format dar.

Unter Linux sammelt Defender für Cloud Überwachungsdatensätze von Linux-Computern mithilfe von „auditd“, einem der gängigsten Linux-Überwachungsframeworks.


## <a name="what-are-the-benefits-of-microsoft-defender-for-servers"></a>Welche Vorteile bietet Microsoft Defender für Server?

Microsoft Defender für Server bietet folgende Funktionen für Bedrohungserkennung und Schutz:

- **Integrierte Lizenz für Microsoft Defender für Endpunkt**: Microsoft Defender für Server enthält [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung. Weitere Informationen finden Sie unter [Schützen Ihrer Endpunkte](integration-defender-for-endpoint.md).

    Wenn Defender für Endpunkt eine Bedrohung erkennt, wird eine Warnung ausgelöst. Die Warnung wird in Defender für Cloud angezeigt. Über Defender für Cloud können Sie auch zur Defender für Endpunkt-Konsole wechseln und eine detaillierte Untersuchung durchführen, um das Ausmaß des Angriffs zu ermitteln. Erfahren Sie mehr zu Microsoft Defender für Endpunkt.

    > [!IMPORTANT]
    > Die Integration von Defender für Cloud in Microsoft Defender für Endpunkt ist standardmäßig aktiviert. Wenn Sie Microsoft Defender für Server aktivieren, erteilen Sie damit also Defender für Cloud die Berechtigung, auf die Daten von Microsoft Defender für Endpunkt im Zusammenhang mit Sicherheitsrisiken, installierter Software und Warnungen für Ihre Endpunkte zuzugreifen.
    >
    > Derzeit bieten wir den Sensor für Linux-Computer in der Vorschauversion an. Weitere Informationen erhalten Sie unter [Schützen Sie Ihre Endpunkte mithilfe der in Defender für Cloud integrierten EDR-Lösung: Microsoft Defender für Endpunkt](integration-defender-for-endpoint.md).

- **Tools zur Sicherheitsrisikobewertung für Computer**: Microsoft Defender für Server umfasst eine Auswahl von Tools zur Ermittlung und Verwaltung von Sicherheitsrisiken für Ihre Computer. Auf den Einstellungsseiten von Defender für Cloud können Sie auswählen, welche dieser Tools auf Ihren Computern bereitgestellt werden sollen, und die gefundenen Sicherheitsrisiken werden in einer Sicherheitsempfehlung angezeigt.

    - **Microsoft Bedrohungs- und Sicherheitsrisikomanagement:** Entdecken Sie mit Microsoft Defender für Endpunkt Sicherheitsrisiken und Fehlkonfigurationen in Echtzeit, ohne dass zusätzliche Agents oder regelmäßige Überprüfungen erforderlich sind. Beim [Bedrohungs- und Sicherheitsrisikomanagement](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt) werden die Sicherheitsrisiken basierend auf der Bedrohungsumgebung, den Erkennungen in Ihrer Organisation, der Vertraulichkeit der Informationen auf anfälligen Geräten und dem geschäftlichen Kontext priorisiert. Weitere Informationen finden Sie unter [Untersuchen von Schwachstellen mit dem Bedrohungs- und Sicherheitsrisikomanagement von Microsoft Defender für Endpunkt](deploy-vulnerability-assessment-tvm.md).

    - **Überprüfung auf Sicherheitsrisiken von Qualys:** Der Scanner von Qualys ist eines der führenden Tools für die Echtzeiterkennung von Sicherheitsrisiken auf Ihren Azure- und Hybrid-VMs. Sie benötigen weder eine Qualys-Lizenz noch ein Qualys-Konto – die Abwicklung erfolgt nahtlos in Defender für Cloud. Erfahren Sie mehr dazu unter [In Defender für Cloud integrierte Qualys-Überprüfung auf Sicherheitsrisiken für Azure- und Hybridcomputer](deploy-vulnerability-assessment-vm.md).

- **Just-In-Time-Zugriff (JIT) auf virtuellen Computer (VM):** Bedrohungsakteure suchen aktiv nach zugänglichen Computern mit offenen Verwaltungsports, z. B. für RDP oder SSH. Alle Ihre virtuellen Computer sind potenzielle Ziele für Angriffe. Wenn eine VM erfolgreich kompromittiert wurde, wird sie als Einstiegspunkt verwendet, um weitere Ressourcen in Ihrer Umgebung anzugreifen.

    Wenn Sie Microsoft Defender für Server aktivieren, können Sie Just-In-Time-VM-Zugriff verwenden, um eingehenden Datenverkehr auf Ihren VMs zu blockieren und dadurch die Gefährdung durch Angriffe zu verringern und bei Bedarf einen einfachen Zugriff für die Herstellung von Verbindungen mit VMs bereitzustellen. Weitere Informationen finden Sie unter [Grundlegendes zum JIT-VM-Zugriff](just-in-time-access-overview.md).

- **Überwachung der Dateiintegrität:** Die Überwachung der Dateiintegrität (File Integrity Monitoring, FIM), auch als Änderungsüberwachung bezeichnet, untersucht unter anderem Dateien und Registrierungen des Betriebssystems sowie Anwendungen auf Änderungen, die auf einen Angriff hindeuten. Eine Vergleichsmethode wird verwendet, um zu bestimmen, ob der aktuelle Status der Datei sich von der letzten Überprüfung der Datei unterscheidet. Sie können diesen Vergleich nutzen, um zu bestimmen, ob gültige oder verdächtige Änderungen an Ihren Dateien vorgenommen wurden.

    Wenn Sie Microsoft Defender für Server aktivieren, können Sie mithilfe von FIM die Integrität von Windows-Dateien, Windows-Registrierungen und Linux-Dateien überprüfen. Weitere Informationen finden Sie unter [Überwachung der Dateiintegrität in Microsoft Defender für Cloud](file-integrity-monitoring-overview.md).

- **Adaptive Anwendungssteuerungen**: Adaptive Anwendungssteuerungen sind eine intelligente und automatisierte Lösung zum Definieren von Positivlisten bekannter, sicherer Anwendungen für Ihre Computer.

    Wenn Sie adaptive Anwendungssteuerungen aktiviert und konfiguriert haben, erhalten Sie Sicherheitswarnungen, wenn eine Anwendung ausgeführt wird, die nicht als sicher definiert ist. Weitere Informationen finden Sie unter [Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers](adaptive-application-controls.md).

- **Adaptive Netzwerkhärtung (ANH):** Der Einsatz von Netzwerksicherheitsgruppen (NSGs) zum Filtern von ein- und ausgehendem Datenverkehr für Ressourcen verbessert den Sicherheitsstatus Ihres Netzwerks. Es gibt jedoch Situationen, in denen es sich bei dem Datenverkehr, der die NSG durchläuft, um eine Teilmenge der definierten NSG-Regeln handelt. In diesen Fällen lässt sich der Sicherheitsstatus durch eine Härtung der NSG-Regeln auf der Grundlage tatsächlicher Datenverkehrsmuster noch weiter verbessern.

    Die adaptive Netzwerkhärtung liefert Empfehlungen zur weiteren Härtung der NSG-Regeln. Dabei kommt ein Machine Learning-Algorithmus zum Einsatz, der Faktoren wie tatsächlichen Datenverkehr, bekannte vertrauenswürdige Konfiguration und Bedrohungsinformationen sowie weitere Anzeichen einer Kompromittierung berücksichtigt und auf der Grundlage dieser Faktoren Empfehlungen abgibt, um nur Datenverkehr von bestimmten IP-/Port-Tupeln zuzulassen. Weitere Informationen finden Sie unter [Verbessern des Netzwerksicherheitsstatus mit adaptiver Netzwerkhärtung](adaptive-network-hardening.md).


- **Docker-Hosthärtung**: Microsoft Defender für Cloud identifiziert nicht verwaltete Container, die auf IaaS-Linux-VMs oder anderen Linux-Computern gehostet werden, auf denen Docker-Container ausgeführt werden. Defender für Cloud bewertet kontinuierlich die Konfigurationen dieser Container. Anschließend werden sie mit dem Docker-Benchmark von Center for Internet Security (CIS) verglichen. Defender für Cloud umfasst den gesamten Regelsatz des CIS-Docker-Benchmarks und benachrichtigt Sie, sobald Ihre Container eine der Kontrollen nicht bestehen. Weitere Informationen finden Sie unter [Härten Ihrer Docker-Hosts](harden-docker-hosts.md).

- **Erkennung dateiloser Angriffe**: Bei dateilosen Angriffen werden schädliche Nutzlasten in den Arbeitsspeicher injiziert, um die Erkennung durch Verfahren zur datenträgerbasierten Überprüfung zu verhindern. Die Nutzlast des Angreifers nistet sich im Arbeitsspeicher von kompromittierten Prozessen ein und führt ein breites Spektrum an schädlichen Aktivitäten aus.

  Die Erkennung dateiloser Angriffe erkennt dank automatisierter forensischer Techniken für den Arbeitsspeicher Toolkits, Techniken und Verhaltensweisen im Zusammenhang mit dateilosen Angriffen. Diese Lösung überprüft zur Laufzeit in regelmäßigen Abständen Ihren Computer und extrahiert Erkenntnisse direkt aus dem Arbeitsspeicher von Prozessen. Zu den spezifischen Erkenntnissen zählt die Ermittlung der folgenden Elemente: 

  - Bekannte Toolkits und Kryptografieminingsoftware 

  - Shellcode, ein kurzer Codeabschnitt, der normalerweise als Nutzlast bei der Ausnutzung eines Softwaresicherheitsrisikos verwendet wird

  - Injektion bösartiger ausführbarer Dateien in den Prozessspeicher

  Bei der Erkennung dateiloser Angriffe werden ausführliche Sicherheitswarnungen generiert, die Beschreibungen mit Prozessmetadaten (etwa zur Netzwerkaktivität) enthalten. Diese Details beschleunigen die Warnungsselektion und die Korrelation und verkürzen die Downstream-Antwortzeit. Dieser Ansatz dient zur Ergänzung ereignisbasierter EDR-Lösungen und ermöglicht ein größeres Erkennungsspektrum.

  Details der Warnungen bei Erkennung dateiloser Angriffe finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-windows).

- **Integration von Linux-auditd-Warnungen mit dem Log Analytics-Agent (nur Linux):** Beim auditd-System handelt es sich um ein Subsystem auf Kernelebene, das Systemaufrufe überwacht. Es filtert sie nach einem angegebenen Regelsatz und schreibt entsprechende Meldungen in einen Socket. Defender für Cloud integriert Funktionen aus dem auditd-Paket im Log Analytics-Agent. Diese Integration ermöglicht das Sammeln von auditd-Ereignissen in allen unterstützten Linux-Distributionen ohne weitere Bedingungen.

    Der Log Analytics-Agent für Linux sammelt überwachte Datensätze und erweitert und aggregiert sie zu Ereignissen. In Defender für Cloud werden ständig neue Analysen hinzugefügt, die Linux-Signale verwenden, um schädliches Verhalten auf cloudbasierten und lokalen Linux-Computern zu erkennen. Diese Analysen umfassen ähnlich wie bei Windows-Funktionen verdächtige Prozesse und Anmeldeversuche, das Laden von Kernmodulen sowie andere Aktivitäten. Diese Aktivitäten können darauf hindeuten, dass ein Computer angegriffen wird oder kompromittiert wurde.  

    Eine Liste der Linux-Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulieren von Warnungen

Sie können Warnungen simulieren, indem Sie eines der folgenden Playbooks herunterladen:

- Für Windows: [Playbook für Microsoft Defender für Cloud: Sicherheitswarnungen](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Für Linux: [Playbook für Microsoft Defender für Cloud: Linux-Erkennungen](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf)




## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über Microsoft Defender für Server erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)

Verwandte Informationen finden Sie auf der folgenden Seite:

- Sie können Warnungen exportieren – unabhängig davon, ob sie von Defender für Cloud generiert oder über ein anderes Sicherheitsprodukt in Defender für Cloud empfangen wurden. Befolgen Sie die Anweisungen unter [Fortlaufendes Exportieren von Security Center-Daten](continuous-export.md), um die Warnungen in Microsoft Sentinel, ein Drittanbieter-SIEM-System oder ein beliebiges anderes externes Tool zu exportieren.
