---
title: Azure Defender für App Service – Vorteile und Features
description: Hier finden Sie Informationen zu den Funktionen von Azure Defender für App Service, und Sie erfahren, wie Sie dieses Tool für Ihr Abonnement aktivieren.
author: memildin
ms.author: memildin
ms.date: 01/25/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 788cce5f8f380c2bcf2fa7c66b06f22bcda167f2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732586"
---
# <a name="protect-your-web-apps-and-apis"></a>Schützen Ihrer Web-Apps und APIs

## <a name="prerequisites"></a>Voraussetzungen

Das Security Center ist nativ in den App Service integriert, wodurch die Notwendigkeit von Bereitstellung und Onboarding entfällt – die Integration ist transparent.

Zum Schützen Ihres Azure App Service-Plans mit Azure Defender für App Service benötigen Sie Folgendes:

- Einen unterstützten App Service-Plan, der dedizierten Computern zugeordnet ist. Unterstützte Pläne sind unter [Verfügbarkeit](#availability) aufgeführt.

- Für Ihr Abonnement aktivierter Azure Defender gemäß Beschreibung in [Schnellstart: Aktivieren von Azure Defender](enable-azure-defender.md).

    > [!TIP]
    > Sie können optional auch einzelne Pläne in Azure Defender aktivieren (beispielsweise Azure Defender für App Service).

## <a name="availability"></a>Verfügbarkeit

| Aspekt                       | Details                                                                                                                                                                                        |
|------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:               | Allgemeine Verfügbarkeit (General Availability, GA)                                                                                                                                                                      |
| Preise:                     | [Azure Defender für App Service](azure-defender.md) wird gemäß [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/) abgerechnet.<br>Die Abrechnung erfolgt gemäß der Gesamtanzahl von Compute-Instanzen in allen Plänen.       |
| Unterstützte App Service-Pläne: | [Alle App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/) werden mit Ausnahme von [Azure Functions im Verbrauchsplan](../azure-functions/functions-scale.md) unterstützt. |
| Clouds:                      | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National/Sovereign (Azure Government, Azure China 21Vianet)                                                     |
|                              |                                                                                                                                                                                                |

## <a name="what-are-the-benefits-of-azure-defender-for-app-service"></a>Welche Vorteile bietet Azure Defender für App Service?

Azure App Service ist eine vollständig verwaltete Plattform zum Entwickeln und Hosten Ihrer Web-Apps und APIs. Da die Plattform vollständig verwaltet ist, müssen Sie sich keinerlei Gedanken über die Infrastruktur machen. Sie bietet Verwaltung, Überwachung sowie betriebliche Erkenntnisse und erfüllt die Anforderungen auf Unternehmensniveau hinsichtlich Leistung, Sicherheit und Konformität. Weitere Informationen finden Sie unter [Azure App Service](https://azure.microsoft.com/services/app-service/).

**Azure Defender für App Service** nutzt die Kapazitäten der Cloud, um Angriffe auf Anwendungen unter App Service zu identifizieren. Angreifer testen Webanwendungen, um Schwachstellen zu suchen und auszunutzen. Bevor Anforderungen für in Azure ausgeführte Anwendungen an bestimmte Umgebungen weitergeleitet werden, durchlaufen sie mehrere Gateways, wo sie jeweils überprüft und protokolliert werden. Diese Daten werden dann verwendet, um Exploits und Angreifer zu identifizieren sowie um neue Muster zu erkennen, die später zur Anwendung kommen.

Wenn Sie Azure Defender für App Service aktivieren, profitieren Sie umgehend von den folgenden Diensten, die im Rahmen dieses Azure Defender-Plans angeboten werden:

- **Schutz:** Security Center bewertet die von Ihrem App Service-Plan abgedeckten Ressourcen und generiert Sicherheitsempfehlungen auf der Grundlage der Ergebnisse. Verwenden Sie die ausführlichen Anweisungen in diesen Empfehlungen, um die Sicherheit Ihrer App Service-Ressourcen zu verbessern.

- **Erkennung:** Azure Defender erkennt verschiedenste Bedrohungen für Ihre App Service-Ressourcen und überwacht dazu Folgendes:
    - Die VM-Instanz, in der Ihre App Service-Instanz ausgeführt wird, sowie die zugehörige Verwaltungsschnittstelle
    - Die Anforderungen und Antworten, die an Ihre und von Ihren App Service-Apps gesendet werden
    - Die zugrunde liegenden Sandboxes und virtuellen Computer
    - Interne App Service-Protokolle (verfügbar dank der Transparenz, über die Azure als Cloudanbieter verfügt)

Als cloudnative Lösung kann Azure Defender Angriffsmethoden für mehrere Ziele identifizieren. Von einem einzelnen Host aus ist es beispielsweise schwierig, einen verteilten Angriff über eine kleine Teilmenge von IP-Adressen zu identifizieren, der sich auf ähnliche Endpunkte auf mehreren Hosts ausbreitet.

Die gemeinsame Betrachtung von Protokolldaten und Infrastruktur liefert umfassende Erkenntnisse: von neu in Umlauf gebrachten Angriffen bis hin zu kompromittierten Kundencomputern. Auch wenn Security Center bereitgestellt wird, nachdem eine Web-App ausgenutzt wurde, können fortlaufende Angriffe möglicherweise erkannt werden.


## <a name="what-threats-can-azure-defender-for-app-service-detect"></a>Welche Bedrohungen können von Azure Defender für App Service erkannt werden?

### <a name="threats-by-mitre-attck-tactics"></a>Bedrohungen durch MITRE ATT&CK-Taktiken

Die Überwachung von Azure Defender umfasst zahlreiche Bedrohungen für Ihre App Service-Ressourcen. Die Warnungen decken nahezu alle MITRE ATT&CK-Taktiken ab – von Bedrohungen vor einem Angriff bis hin zu Command and Control. Azure Defender ermöglicht die Erkennung von Folgendem:

- **Bedrohungen vor einem Angriff:** Defender kann die Ausführung verschiedener Arten von Sicherheitsrisikoüberprüfungen erkennen, die häufig von Angreifern verwendet werden, um Anwendungen auf Schwachstellen zu untersuchen.

- **Bedrohungen beim ersten Zugriff:** Diese Warnungen basieren auf [Microsoft Threat Intelligence](https://go.microsoft.com/fwlink/?linkid=2128684) und lösen eine Warnung aus, wenn über eine bekannte schädliche IP-Adresse eine Verbindung mit der FTP-Schnittstelle Ihrer Azure App Service-Instanz hergestellt wird.

- **Ausführungsbedrohungen:** Defender kann erkennen, wenn versucht wird, Befehle mit hoher Berechtigung oder Linux-Befehle für eine Windows App Service-Instanz auszuführen. Außerdem kann Defender Verhaltensweisen eines dateilosen Angriffs sowie Tools für Digital Currency Mining und viele weitere verdächtige und schädliche Codeausführungsaktivitäten erkennen.

### <a name="dangling-dns-detection"></a>Erkennung verwaister DNS-Einträge

Von Azure Defender für App Service werden auch DNS-Einträge erkannt, die in Ihrer DNS-Registrierungsstelle zurückbleiben, wenn eine App Service-Website eingestellt wird. Diese werden als verwaiste DNS-Einträge bezeichnet. Wenn Sie eine Website entfernen, aber nicht die zugehörige benutzerdefinierte Domäne aus Ihrer DNS-Registrierungsstelle, verweist der DNS-Eintrag auf eine nicht vorhandene Ressource, und Ihre Unterdomäne ist anfällig für eine Übernahme. Ihre DNS-Registrierungsstelle wird von Azure Defender nicht auf *bereits vorhandene* verwaiste DNS-Einträge überprüft. Sie werden benachrichtigt, wenn eine App Service-Website eingestellt und die zugehörige benutzerdefinierte Domäne (DNS-Eintrag) nicht gelöscht wird.

Unterdomänenübernahmen sind eine weit verbreitete Bedrohung mit hohem Schweregrad für Organisationen. Wenn ein Bedrohungsakteur einen verwaisten DNS-Eintrag erkennt, kann er unter der Zieladresse eine eigene Website erstellen. Der für die Domäne der Organisation bestimmte Datenverkehr wird dann an die Website des Bedrohungsakteurs weitergeleitet und kann für ein breites Spektrum an schädlichen Aktivitäten verwendet werden.

Der Schutz vor verwaisten DNS-Einträgen ist unabhängig davon verfügbar, ob Ihre Domänen mit Azure DNS oder mit einer externen Domänenregistrierungsstelle verwaltet werden, und gilt sowohl für App Service unter Windows als auch für App Service unter Linux.

:::image type="content" source="media/defender-for-app-service-introduction/dangling-dns-alert.png" alt-text="Beispiel für eine Azure Defender-Warnung bei Erkennung eines verwaisten DNS-Eintrags. Aktivieren Sie Azure Defender für App Service, um diese und andere Warnungen für Ihre Umgebung zu erhalten." lightbox="media/defender-for-app-service-introduction/dangling-dns-alert.png":::

Weitere Informationen zu verwaisten DNS-Einträgen sowie zur Gefahr von Unterdomänenübernahmen finden Sie unter [Verhindern verwaister DNS-Einträge und Vermeiden von Unterdomänenübernahmen](../security/fundamentals/subdomain-takeover.md).

Eine vollständige Liste der Azure App Service-Warnungen finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azureappserv).

> [!NOTE]
> Von Defender werden möglicherweise keine Warnungen im Zusammenhang mit verwaisten DNS-Einträgen ausgelöst, wenn Ihre benutzerdefinierte Domäne nicht direkt auf eine App Service-Ressource verweist oder wenn der Datenverkehr für Ihre Website von Defender nicht überwacht wurde, seitdem der Schutz vor verwaisten DNS-Einträgen aktiviert wurde. (Der Grund: Es sind keine Protokolle vorhanden, um die benutzerdefinierte Domäne zu identifizieren.)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über Azure Defender für App Service erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](enable-azure-defender.md)

Weitere Informationen finden Sie in den folgenden Artikeln: 

- Befolgen Sie die Anweisungen unter [Streamen von Warnungen an eine SIEM-, SOAR- oder IT-Dienstverwaltungslösung](export-to-siem.md), um die Warnungen zu Azure Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren.
- Eine Liste der Warnungen im Zusammenhang mit Azure Defender für App Service finden Sie in der [Referenztabelle der Warnungen](alerts-reference.md#alerts-azureappserv).
- Weitere Informationen zu App Service-Plänen finden Sie unter [App Service-Pläne](https://azure.microsoft.com/pricing/details/app-service/plans/).