---
title: Härten des Windows- und Linux-Betriebssystems mithilfe der Azure-Sicherheitsbaseline und von Microsoft Defender for Cloud
description: Erfahren Sie, wie Microsoft Defender for Cloud die Gastkonfiguration verwendet, um Ihre Betriebssystemhärtung mit der Anleitung von Azure Security Benchmark zu vergleichen.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 3ac0b2745ac7abe91bc9f1a122e98842ce0e4f0e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526162"
---
# <a name="apply-azure-security-baselines-to-machines"></a>Anwenden von Azure-Sicherheitsbaselines auf Computer

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Um die Angriffsfläche eines Rechners zu verringern und bekannte Risiken zu vermeiden, ist es wichtig, das Betriebssystem (OS) so sicher wie möglich zu konfigurieren.

Der Azure Security Benchmark enthält Richtlinien für die Betriebssystemhärtung, die zu Sicherheitsgrundlagendokumenten für [Windows](../governance/policy/samples/guest-configuration-baseline-windows.md) und [Linux](../governance/policy/samples/guest-configuration-baseline-linux.md) geführt haben.

Verwenden Sie die in diesem Artikel beschriebenen Sicherheitsempfehlungen, um die Maschinen in Ihrer Umgebung zu bewerten und:

- Identifizieren Sie Lücken in den Sicherheitskonfigurationen
- lernen Sie, wie Sie diese Lücken beseitigen können

## <a name="availability"></a>Verfügbarkeit
|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau.<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|Preise:|Kostenlos|
|Voraussetzungen:|Die Rechner müssen (1) Mitglied einer Arbeitsgruppe sein, (2) über die Erweiterung Gastkonfiguration verfügen, (3) eine vom System zugewiesene verwaltete Identität haben und (4) ein unterstütztes Betriebssystem ausführen:<br>• Windows Server 2012, 2012r2, 2016 oder 2019<br>• Ubuntu 14.04, 16.04, 17.04, 18.04 oder 20.04<br>• Debian 7, 8, 9, oder 10<br>• CentOS 7 oder 8<br>• Red Hat Enterprise Linux (RHEL) 7 oder 8<br>• Oracle Linux 7 oder 8<br>• SUSE Linux Enterprise Server 12|
|Erforderliche Rollen und Berechtigungen:|Zur Installation der Gastkonfigurationserweiterung und ihrer Voraussetzungen ist auf den betreffenden Rechnern die Berechtigung **schreiben** erforderlich.<br>Für die **Ansicht** der Empfehlungen und die Untersuchung der Betriebssystem-Basisdaten ist eine **Lese**-Berechtigung auf der Abonnementebene erforderlich.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/no-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-hardening-recommendations"></a>Was sind die Empfehlungen zur Absicherung?

Microsoft Defender for Cloud enthält zwei Empfehlungen, die prüfen, ob die Konfiguration von Windows- und Linux-VMs in Ihrer Umgebung den Konfigurationen gemäß Azure-Sicherheitsbaseline entspricht:

- Für **Windows**-Maschinen sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Windows-Maschinen behoben werden (powered by Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6), indem die Konfiguration mit der [Windows-Sicherheits-Baseline](../governance/policy/samples/guest-configuration-baseline-windows.md) verglichen wird.
- Bei **Linux**-Rechnern vergleicht [Schwachstellen in der Sicherheitskonfiguration auf Ihren Linux-Rechnern sollten behoben werden (mit Hilfe der Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda) die Konfiguration mit der [Linux-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-linux.md).

Diese Empfehlungen verwenden die Gastkonfigurationsfunktion von Azure Policy, um die Betriebssystemkonfiguration einer Maschine mit der im [Azure Security Benchmark](/security/benchmark/azure/overview) definierten Baseline zu vergleichen.

## <a name="compare-machines-in-your-subscriptions-with-the-os-security-baselines"></a>Vergleichen Sie Maschinen in Ihren Abonnements mit den OS-Sicherheitsbaselines

So vergleichen Sie Maschinen mit den OS-Sicherheits-Baselines:
 
1. Öffnen Sie auf den Portalseiten von Defender for Cloud die Seite **Empfehlungen**. 
1. Wählen Sie die entsprechende Empfehlung:
    - Für **Windows**-Rechner, [Schwachstellen in der Sicherheitskonfiguration auf Ihren Windows-Rechnern sollten behoben werden (powered by Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1f655fb7-63ca-4980-91a3-56dbc2b715c6)
    - Für **Linux**-Rechner sollten [Schwachstellen in der Sicherheitskonfiguration auf Ihren Linux-Rechnern behoben werden (mit Hilfe der Gastkonfiguration)](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/8c3d9ad0-3639-4686-9cd2-2b2ab2609bda)

    :::image type="content" source="media/apply-security-baseline/recommendations-baseline.png" alt-text="Die beiden Empfehlungen zum Vergleich der Betriebssystemkonfiguration von Maschinen mit der entsprechenden Azure-Sicherheits-Baseline." lightbox="media/apply-security-baseline/recommendations-baseline.png":::

1. Auf der Seite mit den Empfehlungsdetails können Sie sehen:
    1. Die betroffenen Ressourcen
    1. Die spezifischen Sicherheitsüberprüfungen, die fehlgeschlagen sind.

    :::image type="content" source="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png" alt-text="Empfehlungsdetailseite für die Windows-Empfehlung zu Sicherheitslücken in der Basiskonfiguration von Windows-Rechnern." lightbox="media/apply-security-baseline/recommendation-details-page-vulnerabilities-windows.png":::

1. Um mehr über einen bestimmten Befund zu erfahren, wählen Sie ihn aus.

    :::image type="content" source="media/apply-security-baseline/finding-details.png" alt-text="Mehr über einen bestimmten Befund aus dem Gastkonfigurationsvergleich einer Betriebssystemkonfiguration mit der definierten Sicherheits-Basiskonfiguration erfahren." lightbox="media/apply-security-baseline/finding-details.png":::

1. Andere Untersuchungsmöglichkeiten:

    - Um die Liste der untersuchten Rechner anzuzeigen, öffnen Sie **Betroffene Ressourcen**.
    - Um die Liste der Befunde für einen Rechner zu sehen, wählen Sie einen Rechner auf der Registerkarte **Ungesunde Ressourcen**. Es öffnet sich eine Seite, die nur die Befunde für diesen Rechner auflistet.


## <a name="faq---hardening-an-os-according-to-the-security-baseline"></a>FAQ - Härtung eines Betriebssystems gemäß der Sicherheits-Baseline

- [Wie verteile ich die Voraussetzungen für die Empfehlungen zur Sicherheitskonfiguration?](#how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations)
- [Warum wird ein Rechner als nicht anwendbar angezeigt?](#why-is-a-machine-shown-as-not-applicable)

### <a name="how-do-i-deploy-the-prerequisites-for-the-security-configuration-recommendations"></a>Wie stelle ich die Voraussetzungen für die Empfehlungen zur Sicherheitskonfiguration bereit?

So stellen Sie die Gastkonfigurationserweiterung mit den entsprechenden Voraussetzungen bereit:

- Befolgen Sie für ausgewählte Maschinen die Sicherheitsempfehlung **Die Gastkonfigurationserweiterung sollte auf Ihren Maschinen installiert werden** aus der Sicherheitskontrolle **Implementieren Sie bewährte Sicherheitsverfahren**.

- Weisen Sie bei der Skalierung die Richtlinieninitiative **Voraussetzungen für die Aktivierung von Gastkonfigurationsrichtlinien auf virtuellen Maschinen ein**.


### <a name="why-is-a-machine-shown-as-not-applicable"></a>Warum wird eine Maschine als nicht anwendbar angezeigt?

Die Liste der Ressourcen auf der Registerkarte **Nicht anwendbar** enthält eine Spalte **Grund**. Einige der häufigsten Gründe sind:

| Grund                                                            | Details                                                                                                                                                                        |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Keine Scandaten für die Maschine verfügbar**                         | Es gibt keine Konformitätsergebnisse für diese Maschine in Azure Resource Graph. Alle Konformitätsergebnisse werden von der Guest Configuration-Erweiterung in Azure Resource Graph geschrieben. Sie können die Daten in Azure Resource Graph anhand der Beispielabfragen in [Azure Policy Guest Configuration - sample ARG queries](../governance/policy/samples/resource-graph-samples.md?tabs=azure-cli#azure-policy-guest-configuration) überprüfen.|
| **Gastkonfigurationserweiterung ist nicht auf der Maschine installiert** | Auf dem Rechner fehlt die Guest Configuration-Erweiterung, die eine Voraussetzung für die Bewertung der Übereinstimmung mit der Azure-Sicherheitsgrundlinie ist.                               |
| **Die vom System verwaltete Identität ist auf dem Computer nicht konfiguriert**      | Auf der Maschine muss eine vom System zugewiesene, verwaltete Identität implementiert werden.                                                                                                           |
| **Die Empfehlung ist in der Richtlinie deaktiviert**                      | Die Richtliniendefinition, mit der die Betriebssystem-Baseline bewertet wird, ist in dem Bereich deaktiviert, der den betreffenden Computer umfasst.                                                               |
|                                                                   |                                                                                                                                                                                |

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie gelernt, wie Sie die Empfehlungen von Defender for Cloud zur Gastkonfiguration verwenden können, um die Härtung Ihres Betriebssystems mit der Azure-Sicherheitsbaseline zu vergleichen.

Um mehr über diese Konfigurationseinstellungen zu erfahren, siehe:

- [Windows-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-windows.md)
- [Linux-Sicherheitsbaseline](../governance/policy/samples/guest-configuration-baseline-linux.md)
- [Einführung zum Azure Security-Vergleichstest](/security/benchmark/azure/overview)
