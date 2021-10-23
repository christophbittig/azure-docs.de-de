---
title: Azure Defender für Server – Vorteile und Features
description: Erfahren Sie etwas über die Vorteile und Features von Azure Defender für Server.
author: memildin
ms.author: memildin
ms.date: 09/05/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 1a7b5d1e7ed3c4c1de2c535fb126b67f3ffadecd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711704"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Einführung in Azure Defender für Server

Azure Defender für Server stattet Ihre Windows- und Linux-Computer mit Bedrohungserkennung und erweiterten Schutzmaßnahmen aus.

Unter Windows wird Azure Defender zur Überwachung und zum Schutz Ihrer Windows-basierten Computer in Azure-Dienste integriert. In Security Center werden die Warnungen und Behandlungsvorschläge aus allen diesen Diensten in einem benutzerfreundlichen Format präsentiert.

Unter Linux sammelt Azure Defender Überwachungsdatensätze von Linux-Computern mithilfe von auditd, einem der am häufigsten verwendeten Linux-Überwachungsframeworks.


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Welche Vorteile bietet die Nutzung von Azure Defender für Server?

Zu den Funktionen für Bedrohungserkennung und Schutz von Azure Defender für Server gehören:

- **Integrierte Lizenz für Microsoft Defender für Endpunkt**: Azure Defender für Server enthält [Microsoft Defender für Endpunkt](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Dadurch stehen umfassende EDR-Funktionen (Endpoint Detection and Response; Endpunkterkennung und -reaktion) zur Verfügung. Weitere Informationen finden Sie unter [Schützen Ihrer Endpunkte](security-center-wdatp.md).

    Wenn Defender für Endpunkt eine Bedrohung erkennt, wird eine Warnung ausgelöst. Die Warnung wird in Security Center angezeigt. Über Security Center können Sie auch zur Defender für Endpunkt-Konsole wechseln und eine ausführliche Untersuchung durchführen, um das Ausmaß des Angriffs zu ermitteln. Erfahren Sie mehr zu Microsoft Defender für Endpunkt.

    > [!IMPORTANT]
    > Die Integration von Security Center mit Microsoft Defender für Endpunkt ist standardmäßig aktiviert. Wenn Sie Azure Defender aktivieren, erteilen Sie damit also die Zustimmung für Azure Defender für Server, auf die Daten von Microsoft Defender für Endpoint im Zusammenhang mit Sicherheitsrisiken, installierter Software und Warnungen für Ihre Endpunkte zuzugreifen.
    >
    > Derzeit bieten wir den Sensor für Linux-Computer in der Vorschauversion an. Weitere Informationen finden Sie unter [Schützen Sie Ihre Endpunkte mit der in Security Center integrierten EDR-Lösung: Microsoft Defender für den Endpunkt](security-center-wdatp.md).

- **Tools zur Sicherheitsrisikobewertung für Computer:** Azure Defender für Server umfasst eine Auswahl von Tools zur Ermittlung und Verwaltung von Sicherheitsrisiken für Ihre Computer. Auf den Security Center-Einstellungsseiten können Sie auswählen, welche dieser Tools auf Ihren Computern bereitgestellt werden sollen, und die gefundenen Sicherheitsrisiken werden in einer Sicherheitsempfehlung angezeigt.

    - **Microsoft Bedrohungs- und Sicherheitsrisikomanagement:** Entdecken Sie mit Microsoft Defender für Endpunkt Sicherheitsrisiken und Fehlkonfigurationen in Echtzeit, ohne dass zusätzliche Agents oder regelmäßige Überprüfungen erforderlich sind. Beim [Bedrohungs- und Sicherheitsrisikomanagement](/microsoft-365/security/defender-endpoint/next-gen-threat-and-vuln-mgt) werden die Sicherheitsrisiken basierend auf der Bedrohungsumgebung, den Erkennungen in Ihrer Organisation, der Vertraulichkeit der Informationen auf anfälligen Geräten und dem geschäftlichen Kontext priorisiert. Weitere Informationen finden Sie unter [Untersuchen von Schwachstellen mit dem Bedrohungs- und Sicherheitsrisikomanagement von Microsoft Defender für Endpunkt](deploy-vulnerability-assessment-tvm.md).

    - **Überprüfung auf Sicherheitsrisiken von Qualys:** Der Scanner von Qualys ist eines der führenden Tools für die Echtzeiterkennung von Sicherheitsrisiken auf Ihren Azure- und Hybrid-VMs. Sie benötigen keine Qualys-Lizenz und auch kein Qualys-Konto – alles erfolgt nahtlos innerhalb von Security Center. Weitere Informationen finden Sie unter [Integrierte Azure Defender-Lösung zur Sicherheitsrisikobewertung für Azure- und Hybridcomputer](deploy-vulnerability-assessment-vm.md).

- **Just-In-Time-Zugriff (JIT) auf virtuellen Computer (VM):** Bedrohungsakteure suchen aktiv nach zugänglichen Computern mit offenen Verwaltungsports, z. B. für RDP oder SSH. Alle Ihre virtuellen Computer sind potenzielle Ziele für Angriffe. Wenn eine VM erfolgreich kompromittiert wurde, wird sie als Einstiegspunkt verwendet, um weitere Ressourcen in Ihrer Umgebung anzugreifen.

    Wenn Sie Azure Defender für Server aktivieren, können Sie Just-In-Time-VM-Zugriff verwenden, um eingehenden Datenverkehr auf Ihren VMs zu blockieren und dadurch die Gefährdung durch Angriffe zu verringern und bei Bedarf einen einfachen Zugriff für das Verbinden mit VMs bereitzustellen. Weitere Informationen finden Sie unter [Grundlegendes zum JIT-VM-Zugriff](just-in-time-explained.md).

- **Überwachung der Dateiintegrität:** Die Überwachung der Dateiintegrität (File Integrity Monitoring, FIM), auch als Änderungsüberwachung bezeichnet, untersucht unter anderem Dateien und Registrierungen des Betriebssystems sowie Anwendungen auf Änderungen, die auf einen Angriff hindeuten. Eine Vergleichsmethode wird verwendet, um zu bestimmen, ob der aktuelle Status der Datei sich von der letzten Überprüfung der Datei unterscheidet. Sie können diesen Vergleich nutzen, um zu bestimmen, ob gültige oder verdächtige Änderungen an Ihren Dateien vorgenommen wurden.

    Wenn Sie Azure Defender für Server aktivieren, können Sie mithilfe von FIM die Integrität von Windows-Dateien, Windows-Registrierungen und Linux-Dateien überprüfen. Weitere Informationen finden Sie unter [Überwachung der Dateiintegrität in Azure Security Center](security-center-file-integrity-monitoring.md).

- **Adaptive Anwendungssteuerungen**: Adaptive Anwendungssteuerungen sind eine intelligente und automatisierte Lösung zum Definieren von Positivlisten bekannter, sicherer Anwendungen für Ihre Computer.

    Wenn Sie adaptive Anwendungssteuerungen aktiviert und konfiguriert haben, erhalten Sie Sicherheitswarnungen, wenn eine Anwendung ausgeführt wird, die nicht als sicher definiert ist. Weitere Informationen finden Sie unter [Verwenden von adaptiven Anwendungssteuerungen zum Verringern der Angriffsfläche Ihres Computers](security-center-adaptive-application.md).

- **Adaptive Netzwerkhärtung (ANH):** Der Einsatz von Netzwerksicherheitsgruppen (NSGs) zum Filtern von ein- und ausgehendem Datenverkehr für Ressourcen verbessert den Sicherheitsstatus Ihres Netzwerks. Es gibt jedoch Situationen, in denen es sich bei dem Datenverkehr, der die NSG durchläuft, um eine Teilmenge der definierten NSG-Regeln handelt. In diesen Fällen lässt sich der Sicherheitsstatus durch eine Härtung der NSG-Regeln auf der Grundlage tatsächlicher Datenverkehrsmuster noch weiter verbessern.

    Die adaptive Netzwerkhärtung liefert Empfehlungen zur weiteren Härtung der NSG-Regeln. Dabei kommt ein Machine Learning-Algorithmus zum Einsatz, der Faktoren wie tatsächlichen Datenverkehr, bekannte vertrauenswürdige Konfiguration und Bedrohungsinformationen sowie weitere Anzeichen einer Kompromittierung berücksichtigt und auf der Grundlage dieser Faktoren Empfehlungen abgibt, um nur Datenverkehr von bestimmten IP-/Port-Tupeln zuzulassen. Weitere Informationen finden Sie unter [Verbessern des Netzwerksicherheitsstatus mit adaptiver Netzwerkhärtung](security-center-adaptive-network-hardening.md).


- **Docker-Hosthärtung:** Azure Security Center identifiziert nicht verwaltete Container, die auf IaaS-Linux-VMs oder anderen Linux-Computern gehostet werden, auf denen Docker-Container ausgeführt werden. Security Center bewertet kontinuierlich die Konfigurationen dieser Container. Anschließend werden sie mit dem Docker-Benchmark von Center for Internet Security (CIS) verglichen. Security Center umfasst den gesamten Regelsatz des CIS-Docker-Benchmark und benachrichtigt Sie, sobald Ihre Container eine der Steuerungen nicht erfüllen. Weitere Informationen finden Sie unter [Härten Ihrer Docker-Hosts](harden-docker-hosts.md).

- **Erkennung dateiloser Angriffe**: Bei dateilosen Angriffen werden schädliche Nutzlasten in den Arbeitsspeicher injiziert, um die Erkennung durch Verfahren zur datenträgerbasierten Überprüfung zu verhindern. Die Nutzlast des Angreifers nistet sich im Arbeitsspeicher von kompromittierten Prozessen ein und führt ein breites Spektrum an schädlichen Aktivitäten aus.

  Die Erkennung dateiloser Angriffe erkennt dank automatisierter forensischer Techniken für den Arbeitsspeicher Toolkits, Techniken und Verhaltensweisen im Zusammenhang mit dateilosen Angriffen. Diese Lösung überprüft zur Laufzeit in regelmäßigen Abständen Ihren Computer und extrahiert Erkenntnisse direkt aus dem Arbeitsspeicher von Prozessen. Zu den spezifischen Erkenntnissen zählt die Ermittlung der folgenden Elemente: 

  - Bekannte Toolkits und Kryptografieminingsoftware 

  - Shellcode, ein kurzer Codeabschnitt, der normalerweise als Nutzlast bei der Ausnutzung eines Softwaresicherheitsrisikos verwendet wird

  - Injektion bösartiger ausführbarer Dateien in den Prozessspeicher

  Bei der Erkennung dateiloser Angriffe werden ausführliche Sicherheitswarnungen generiert, die Beschreibungen mit Prozessmetadaten (etwa zur Netzwerkaktivität) enthalten. Diese Details beschleunigen die Warnungsselektion und die Korrelation und verkürzen die Downstream-Antwortzeit. Dieser Ansatz dient zur Ergänzung ereignisbasierter EDR-Lösungen und ermöglicht ein größeres Erkennungsspektrum.

  Details der Warnungen bei Erkennung dateiloser Angriffe finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-windows).

- **Integration von Linux-auditd-Warnungen mit dem Log Analytics-Agent (nur Linux):** Beim auditd-System handelt es sich um ein Subsystem auf Kernelebene, das Systemaufrufe überwacht. Es filtert sie nach einem angegebenen Regelsatz und schreibt entsprechende Meldungen in einen Socket. Security Center integriert Funktionen aus dem auditd-Paket im Log Analytics-Agent. Diese Integration ermöglicht das Sammeln von auditd-Ereignissen in allen unterstützten Linux-Distributionen ohne weitere Bedingungen.

    Der Log Analytics-Agent für Linux sammelt überwachte Datensätze und erweitert und aggregiert sie zu Ereignissen. In Security Center werden ständig neue Analysen hinzugefügt, die Linux-Signale verwenden, um schädliches Verhalten auf cloudbasierten und lokalen Linux-Computern zu erkennen. Diese Analysen umfassen ähnlich wie bei Windows-Funktionen verdächtige Prozesse und Anmeldeversuche, das Laden von Kernmodulen sowie andere Aktivitäten. Diese Aktivitäten können darauf hindeuten, dass ein Computer angegriffen wird oder kompromittiert wurde.  

    Eine Liste der Linux-Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-linux).


## <a name="simulating-alerts"></a>Simulieren von Warnungen

Sie können Warnungen simulieren, indem Sie eines der folgenden Playbooks herunterladen:

- Windows: [Azure Security Center-Playbook: Sicherheitswarnungen](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Linux: [Azure Security Center-Playbook: Azure Security Center-Playbook: Linux Detections (Linux-Erkennungsfunktionen)](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf) herunterladen.




## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Azure Defender für Server erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](enable-azure-defender.md)

Verwandte Informationen finden Sie auf der folgenden Seite:

- Sie können Warnungen exportieren, und zwar unabhängig davon, ob sie von Security Center generiert oder von Security Center über ein anderes Sicherheitsprodukt empfangen wurden. Befolgen Sie die Anweisungen unter [Exportieren von Sicherheitswarnungen und -empfehlungen (Vorschau)](continuous-export.md), um die Warnungen zu Azure Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren.