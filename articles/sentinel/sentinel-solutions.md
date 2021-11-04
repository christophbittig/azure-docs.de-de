---
title: Informationen zu Azure Sentinel-Inhalt und -Lösungen | Microsoft-Dokumentation
description: In diesem Artikel werden Azure Sentinel-Inhalte und -Lösungen beschrieben, die Kunden verwenden können, um Datenanalysetools zu finden, die zusammen mit Datenconnectoren gepackt sind.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 38508b591d79e0dee910468f595fa144353574c9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064067"
---
# <a name="about-azure-sentinel-content-and-solutions"></a>Informationen zu Azure Sentinel-Inhalt und -Lösungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Die Azure Sentinel **Content Hub**-Erfahrung befindet sich derzeit genau wie alle einzelnen Lösungspakete in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Azure Sentinel-*Inhalte* sind Security Information & Event Management (SIEM)-Inhalte, die es Kunden ermöglichen, Daten zu erfassen, zu überwachen, zu warnen, zu suchen, zu untersuchen, zu reagieren und sich mit verschiedenen Produkten, Plattformen und Diensten in Azure Sentinel zu verbinden.

Inhalte in Azure Sentinel schließen die folgenden Typen ein:

- **[Datenconnectoren](connect-data-sources.md)** ermöglichen die Protokollaufnahme aus verschiedenen Quellen in Azure Sentinel
- **[Parser bieten](normalization-about-parsers.md)** Protokollformatierung/-transformation in [ASIM](normalization.md)-Formate und unterstützen die Verwendung über Azure Sentinel Inhaltstypen und Szenarien hinweg
- **[Arbeitsmappen bieten](get-visibility.md)** Überwachung, Visualisierung und Interaktivität mit Daten in Azure Sentinel, um aussagekräftige Erkenntnisse für Benutzer hervorzuheben
- **[Analyseregeln stellen](detect-threats-built-in.md)** Warnungen zur Verfügung, die auf relevante SOC-Aktionen über Vorfälle verweisen
- **[Hunting-Abfragen](hunting.md)** werden von SOC-Teams verwendet, um proaktiv nach Bedrohungen in der Azure Sentinel zu suchen
- **[Notebooks](notebooks.md)** unterstützt SOC-Teams bei der Verwendung erweiterter Hunting-Features in Jupyter und Azure Notebooks
- **[Watchlists](watchlists.md)** unterstützen die Erfassung *bestimmter* Daten für eine verbesserte Bedrohungserkennung und geringere Warnungsermüdung
- **[Playbooks und benutzerdefinierte Azure Logic Apps Connectoren](automate-responses-with-playbooks.md)** bieten Features für automatisierte Untersuchungen, Korrekturen und Reaktionsszenarien in Azure Sentinel

Azure Sentinel-*Lösungen* sind Pakete mit Azure Sentinel-Inhalten oder Azure Sentinel-API-Integrationen, die ein end-to-End-Produkt-, Domänen- oder Branchenszenario in der Azure Sentinel erfüllen.

> [!TIP]
> Sie können entweder vorkonfigurierte Inhalte an Ihre eigenen Anforderungen anpassen, oder Sie können Ihre eigene Lösung mit Inhalten erstellen, die Sie für andere Personen in der Community freigeben können. Weitere Informationen finden Sie im [Azure Sentinel Lösungserstellungsleitfaden](https://aka.ms/sentinelsolutionsbuildguide) für die Erstellung und Veröffentlichung von Lösungen.
>
## <a name="discover-and-manage-azure-sentinel-content"></a>Entdecken und Verwalten Sie Azure Sentinel Inhalte

Verwenden Sie Azure Sentinel **Content Hub**, um vorkonfigurierten (integrierten) Inhalt zentral zu entdecken und zu installieren.

Der Azure Sentinel Content Hub bietet Produktauffindbarkeit, Bereitstellung in einem Schritt und Aktivierung von End-to-End-Produkt-, Domänen- und/oder vertikalen, vorkonfigurierten Lösungen und Inhalten in Azure Sentinel.

- Im **Content Hub** können Sie nach [Kategorien](#azure-sentinel-out-of-the-box-content-and-solution-categories) und anderen Parametern filtern oder die leistungsstarke Textsuche verwenden, um den Inhalt zu finden, der am besten für die Anforderungen Ihrer Organisation geeignet ist. Der **Content Hub** gibt auch das [Support-Modell](#azure-sentinel-out-of-the-box-content-and-solution-support-models) an, das auf die einzelnen Inhaltsteile angewandt wird, da einige Inhalte von Microsoft und andere von Partnern oder der Community verwaltet werden.

    Verwalten [Sie Updates für vorkonfigurierte Inhalte](sentinel-solutions-deploy.md#install-or-update-a-solution) über den Azure Sentinel **Content Hub** und für benutzerdefinierte Inhalte über die **Repositorys**-Seite.

- Passen Sie vorkonfigurierte Inhalte an Ihre eigenen Anforderungen an, oder erstellen Sie benutzerdefinierte Inhalte, einschließlich Analyseregeln, Hunting-Abfragen, Notebooks, Arbeitsmappen und mehr. Verwalten Sie Ihre benutzerdefinierten Inhalte direkt in Ihrem Azure Sentinel-Arbeitsbereich, über die [Azure Sentinel-API](/rest/api/securityinsights/)oder in Ihrem eigenen Quellcodeverwaltungsrepository über die Azure Sentinel [Repositorys](ci-cd.md)-Seite.

### <a name="why-content-hub-and-solutions"></a>Warum Content Hub und Lösungen?

Azure Sentinel-*Lösungen* sind gepackte Inhalte oder Integrationen, die einen End-to-End-Produktwert für eine oder mehrere Domänen- oder vertikale Szenarien liefern.

Die Lösungserfahrung ist wird vom [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) für die Auffindbarkeit und die Bereitstellung von Lösungen angetrieben. Weitere Informationen finden Sie im [Azure Sentinel Lösungserstellungsleitfaden](https://aka.ms/sentinelsolutionsbuildguide) für die Erstellung und Veröffentlichung von Lösungen.

Azure Sentinel-Lösungen bieten Erkennbarkeit innerhalb von Produkten, Bereitstellung in einem Schritt und Aktivierung von End-to-End-Produkt-, Domänen- und/oder vertikale Szenarien in Azure Sentinel. Diese Erfahrung wird durch  zur Erkennbarkeit, Bereitstellung und Aktivierung von für Lösungen sowie durch  zur die Dokumenterstellung und Veröffentlichung von Lösungen unterstützt.

- **Gepackte Inhalte** sind Sammlungen von einem oder mehr Teilen von Azure Sentinel-Inhalten, z. B. Datenconnectoren, Arbeitsmappen, Analyseregeln, Playbooks, Hunting-Abfragen, Watchlists, Parser und mehr.

- **Integrationen** umfassen Dienste oder Tools, die mithilfe von Azure Sentinel- oder Azure Log Analytics-APIs erstellt wurden, die Integrationen zwischen Azure und vorhandenen Kundenanwendungen unterstützt oder Daten, Abfragen und mehr von diesen Anwendungen in Azure Sentinel migriert.

Sie können Lösungen auch verwenden, um Pakete von vorkonfigurierten Inhalten in einem einzigen Schritt zu installieren, wo der Inhalt häufig sofort verwendet werden kann. Anbieter und Partner können Lösungen verwenden, um Investitionen zu produktisieren, indem sie einen kombinierten Produkt-, Domänen- oder vertikalen Nutzen bereitstellen.

Verwenden Sie den Content Hub, um Lösungen und vorkonfigurierte Inhalte auf szenariogesteuerte Weise zentral zu entdecken und bereitzustellen.

Weitere Informationen finden Sie unter:

- [Zentrales Entdecken und Bereitstellen von vorkonfigurierten Azure Sentinel-Inhalten und -Lösungen](sentinel-solutions-deploy.md)
- [Azure Sentinel Content Hub-Katalog](sentinel-solutions-catalog.md)

## <a name="azure-sentinel-out-of-the-box-content-and-solution-categories"></a>Azure Sentinel vorkonfigurierte Inhalts- und Lösungskategorien

Azure Sentinel vorkonfigurierter Inhalt kann mit mindestens einer der folgenden Kategorien angewendet werden. Wählen Sie **im Content Hub** die Kategorien aus, die Sie anzeigen möchten, um den angezeigten Inhalt zu ändern.

### <a name="domain-categories"></a>Domänenkategorien

| Kategoriename  | BESCHREIBUNG |
| ---------- | ----------------------- |
| **Anwendung**  | Web-, serverbasierte, SaaS-, Datenbank-, Kommunikations- oder Produktivitätsworkload          |
| **Cloudanbieter**  | Clouddienst|
| **Compliance**   | Complianceprodukt, -dienste und -protokolle  |
| **DevOps**       | Entwicklungsbetriebstools und -dienste    |
| **Identität**     | Identitätsdienstanbieter und -integrationen     |
| **Internet der Dinge (IoT)**    | IoT, OT-Geräte und -Infrastruktur, Industriekontrolldienste                   |
| **IT-Betrieb**| IT-Verwaltung von Produkten und Diensten   |
| **Migration**    | Migrationsumsetzung Produkte, Dienste und               |
| **Netzwerk**   | Netzwerkprodukte, -dienste und -tools    |
| **Plattform**     | Azure Sentinel generische oder Frameworkkomponenten, Cloudinfrastruktur und Plattform|
| **Sicherheit - Andere**   | Andere Sicherheitsprodukte und -dienste ohne andere eindeutige Kategorie           |
| **Sicherheit - Threat Intelligence**  | Threat Intelligence Plattformen, Feeds, Produkte und Dienste        |
| **Sicherheit - Bedrohungsschutz**   | Bedrohungsschutz, E-Mail-Schutz sowie XDR- und Endpoint-Protection-Produkte und -Dienste     |
| **Sicherheit – 0-Day-Sicherheitsrisiko**   | Spezielle Lösungen für Zero-Day-Sicherheitsrisikoangriffe wie [Nobelium](/azure/security/fundamentals/recover-from-identity-compromise) |
| **Sicherheit – Automatisierung (SOAR)**   | Sicherheitsautomatisierungen, SOAR (Security Operations and Automated Responses), Sicherheitsvorgänge sowie Produkte und Dienste für die Reaktion auf Vorfälle.   |
| **Sicherheit - Cloudsicherheit** | CASB (Cloud Access Service Broker), CWPP (Cloud Workload Protection Platforms), CSPM (Cloud Security Posture Management und andere Cloud Security-Produkte und -Dienste |
| **Sicherheit – Informationsschutz**   | Produkte und Dienste zum Schutz von Informationen und Dokumenten|
| **Sicherheit – Insider-Bedrohung**  | Insider Threat And User and Entity Behavioral Analytics (UEBA) für Sicherheitsprodukte und -dienste                |
| **Sicherheit – Netzwerk**    | Sicherheitsnetzwerkgeräte, Firewall, NDR (Netzwerkerkennung und -antwort), NIDP (Netzwerkangriffs- und Erkennungsschutz) und Netzwerkpaketerfassung   |
| **Sicherheit – Sicherheitsrisiko-Management** | Produkte und Dienste für die Verwaltung von Sicherheitsrisiken                    |
| **Storage**      | Dateispeicher und Dateifreigabeprodukte und -dienste                 |
| **Training und Tutorials**  | Schulungs-, Tutorials- und Onboardingressourcen |
| **Benutzerverhalten (UEBA)**    | Analyseprodukte und -dienste für Benutzerverhalten|
| | |

### <a name="industry-vertical-categories"></a>Branchen vertikale Kategorien

| Kategoriename  | BESCHREIBUNG |
| ---------- | ----------------------- |
| **Luft- und Raumfahrt**  | Produkte, Dienste und Inhalte, die spezifisch für die Luftfahrbranche sind |
| **Education**    | Produkte, Dienste und Inhalte, die spezifisch für die Bildungsbranche sind   |
| **Finanzen**      | Produkte, Dienste und Inhalte, die spezifisch für die Finanzbranche sind     |
| **Gesundheitswesen**   | Produkte, Dienste und Inhalte, die spezifisch für die Gesundheitsbranche sind  |
| **Manufacturing** | Produkte, Dienste und Inhalte, die spezifisch für die Fertigungsindustrie sind |
| **Retail (Einzelhandel)**       | Produkte, Dienste und Inhalte, die spezifisch für die Einzelhandelsbranche sind       |
| | |

## <a name="azure-sentinel-out-of-the-box-content-and-solution-support-models"></a>Azure Sentinel vorkonfigurierte Inhalts- und Lösungsunterstützungsmodelle

Sowohl Microsoft als auch andere Organisationen erstellen Azure Sentinel vorkonfigurierte Inhalte und Lösungen. Jeder Teil der vorkonfigurierten Inhalte oder Lösungen verfügt über einen der folgenden Unterstützungstypen:

| Unterstützungsmodell  | BESCHREIBUNG |
| ---------- | ----------------------- |
| **Von Microsoft unterstützt**| Gilt für: <br>– Inhalte/Lösungen, bei denen Microsoft der Datenanbieter (falls relevant) und Autor ist. <br> - Einige von Microsoft erstellte Datenconnectoren für Nicht-Microsoft-Datenquellen. <br><br>    Microsoft unterstützt und verwaltet Inhalte/Lösungen in diesem Unterstützungsmodell gemäß der [Microsoft Azure Supportpläne.](https://azure.microsoft.com/support/options/#overview) <br>Partner oder die Community unterstützen Inhalte/Lösungen, die von einer anderen Partei als Microsoft erstellt werden.|
|**Von Partnern unterstützt** | Gilt für Inhalte/Lösungen, die von anderen Parteien als Microsoft erstellt wurden.  <br><br>   Das Partnerunternehmen bietet Support oder Wartung für diese Inhalts-/Lösungsteile. Das Partnerunternehmen kann ein unabhängiger Softwarehersteller (ISV), ein Anbieter verwalteter Dienste (Managed Service Provider, MSP/MSSP), ein Systemintegrator (SI) oder eine beliebige Organisation sein, deren Kontaktinformationen auf der Azure Sentinel-Seite für die gewählten Inhalte/Lösungen bereitgestellt werden.<br><br>    Wenden Sie sich bei Problemen mit einer vom Partner unterstützten Lösung an den angegebenen Supportkontakt.|
|**Von der Community unterstützt** |Gilt für Inhalte/Lösungen, die von Microsoft oder Partnerentwicklern erstellt wurden und für die keine Kontaktinformationen für Support und Wartung in Azure Sentinel hinterlegt sind.<br><br>    Bei Fragen oder Problemen mit diesen Lösungen können Sie in der [Azure Sentinel GitHub-Community](https://aka.ms/threathunters) [ein Problem erstellen](https://github.com/Azure/Azure-Sentinel/issues/new/choose). |
| | |


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie mehr über die Azure Sentinel erfahren haben, beginnen Sie mit der Verwaltung von Inhalten und Lösungen in Ihrem Azure Sentinel Arbeitsbereich.

Entdecken und installieren Sie Lösungen aus dem Azure Sentinel **Content Hub**. Weitere Informationen finden Sie unter:

- [Zentrales Entdecken und Bereitstellen von vorkonfigurierten Azure Sentinel-Inhalten und -Lösungen (Öffentliche Vorschauversion)](sentinel-solutions-deploy.md)
- [Azure Sentinel Content Hub-Katalog](sentinel-solutions-catalog.md)
- [Datenconnectors für Azure Sentinel](connect-data-sources.md)
- [Ermitteln des richtigen Azure Sentinel-Datenconnectors](data-connectors-reference.md)
