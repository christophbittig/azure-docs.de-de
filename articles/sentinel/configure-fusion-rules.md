---
title: Konfigurieren mehrstufiger Angriffserkennungsregeln (Fusion) in Microsoft Sentinel
description: Erstellen und konfigurieren Sie Angriffserkennungsregeln basierend auf der Fusion-Technologie in Microsoft Sentinel.
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
ms.openlocfilehash: 4c4ea2e5c8376a6dd1c7281251805dfa06c0169c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519703"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-microsoft-sentinel"></a>Konfigurieren mehrstufiger Angriffserkennungsregeln (Fusion) in Microsoft Sentinel

> [!IMPORTANT]
> Die neue Version der Fusion-Analyseregel befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel verwendet Fusion, eine Korrelations-Engine, die auf skalierbaren Machine Learning-Algorithmen basiert, um automatisch mehrstufige Angriffe zu erkennen, indem Kombinationen aus anomalem Verhalten und verdächtigen Aktivitäten identifiziert werden, die in verschiedenen Phasen der Kill Chain beobachtet werden. Auf der Grundlage dieser Entdeckungen generiert Microsoft Sentinel Incidents, die auf andere Weise nur schwer abgefangen werden können. Diese Incidents umfassen mindestens zwei Warnungen oder Aktivitäten. Standardmäßig weisen diese Incidents ein geringes Volumen, eine hohe Qualität und einen hohen Schweregrad auf.

Diese Erkennungstechnologie ist für Ihre Umgebung angepasst und bewirkt nicht nur eine Reduzierung der [False Positive](false-positives.md)-Rate, sondern kann Angriffe auch mit eingeschränkten oder fehlenden Informationen erkennen.

## <a name="configure-fusion-rules"></a>Fusion-Regeln konfigurieren

Diese Erkennung ist in Microsoft Sentinel standardmäßig aktiviert. Um seinen Status zu überprüfen oder zu ändern, verwenden Sie die folgenden Anweisungen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und geben Sie **Microsoft Sentinel** ein.

1. Klicken Sie im Microsoft Sentinel-Navigationsmenü auf **Analytics**.

1. Wählen Sie die Registerkarte **Aktive Regeln** aus. Suchen Sie dann in der Spalte **NAME** die Option **Erweiterte mehrstufige Angriffserkennung** aus, indem Sie die Liste nach dem Regeltyp **Fusion** filtern. Überprüfen Sie in der Spalte **STATUS**, ob diese Erkennung aktiviert oder deaktiviert ist.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Screenshot der Fusion-Analyseregel." lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. Wählen Sie zum Ändern des Status diesen Eintrag und im Vorschaubereich **Erweiterte Erkennung von mehrstufigen Angriffen** die Option **Bearbeiten** aus.

1. Werfen Sie auf der Registerkarte **Allgemein** des **Assistenten für Analytics-Regeln** einen Blick auf den Status (Aktiviert/Deaktiviert) oder ändern Sie ihn bei Bedarf.

    Wenn Sie den Status geändert haben, aber keine weiteren Änderungen vornehmen möchten, wählen Sie die Registerkarte **Überprüfen und aktualisieren** und wählen Sie **Speichern**.

    Um die Fusion-Erkennungsregel weiter zu konfigurieren, wählen Sie **Weiter: Fusion konfigurieren**.

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Screenshot der Fusion-Regelkonfiguration." lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Quellsignale für die Fusion-Erkennung konfigurieren**: Wir empfehlen Ihnen, alle aufgelisteten Quellsignale mit allen Schweregraden einzubeziehen, um das beste Ergebnis zu erzielen. Standardmäßig sind sie bereits alle enthalten, aber Sie haben die Möglichkeit, Änderungen auf folgende Weise vorzunehmen:

    > [!NOTE]
    > Wenn Sie ein bestimmtes Quellsignal oder einen Alarm-Schweregrad ausschließen, werden Fusion-Erkennungen, die auf Signalen von dieser Quelle oder auf Warnungen mit diesem Schweregrad beruhen, nicht ausgelöst. 
    
    - **Signale von Fusion-Erkennungen ausschließen**, einschließlich Anomalien, Warnungen von verschiedenen Anbietern und Rohprotokollen.
     
        *Anwendungsfall:* Wenn Sie eine bestimmte Signalquelle testen, von der bekannt ist, dass sie verrauschte Warnungen erzeugt, können Sie die Signale dieser bestimmten Signalquelle für Fusion-Erkennungen vorübergehend deaktivieren.

    - **Warnungsschweregrad für jeden Anbieter konfigurieren**: Das Fusion ML-Modell korreliert Signale mit niedriger Genauigkeit in einem einzelnen Vorfall mit hohem Schweregrad basierend auf anomalen Signalen in der Kill-Chain aus mehreren Datenquellen. In Fusion enthaltene Warnungen haben im Allgemeinen einen niedrigeren Schweregrad (mittel, niedrig, informativ), aber gelegentlich sind relevante Warnungen mit hohem Schweregrad enthalten.
     
        *Anwendungsfall:* Wenn Sie einen separaten Prozess für die Triage und Untersuchung von Warnungen mit hohem Schweregrad haben und diese Warnungen nicht in Fusion aufnehmen möchten, können Sie die Quellsignale so konfigurieren, dass Warnungen mit hohem Schweregrad von Fusion-Erkennungen ausgeschlossen werden. 



    - **Ausschließen bestimmter Erkennungsmuster von der Fusion-Erkennung**. Bestimmte Fusion-Erkennungen sind möglicherweise nicht auf Ihre Umgebung anwendbar oder neigen dazu, falsch positive Ergebnisse zu generieren. Wenn Sie ein bestimmtes Fusion-Erkennungsmuster ausschließen möchten, befolgen Sie die folgenden Anweisungen:

        1. Suchen und öffnen Sie einen Fusion-Vorfall der Art, die Sie ausschließen möchten.

        1. Wählen Sie im Abschnitt **Beschreibung** die Option **Mehr anzeigen** aus.

        1. Wählen Sie unter **Dieses spezifische Erkennungsmuster ausschließen** den **Ausschlusslink** aus, der Sie zur Registerkarte **Fusion konfigurieren** im Analyseregel-Assistenten weiterleitet.

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Screenshot des Fusion-Vorfalls. Wählen Sie den Ausschlusslink aus.":::

        Auf der Registerkarte **Fusion konfigurieren** sehen Sie, dass das Erkennungsmuster – eine Kombination aus Warnungen und Anomalien in einem Fusion-Vorfall – zusammen mit dem Zeitpunkt, zu dem das Erkennungsmuster hinzugefügt wurde, zur Ausschlussliste hinzugefügt wurde.

        Sie können ein ausgeschlossenes Erkennungsmuster jederzeit entfernen, indem Sie das Mülleimer-Symbol auf diesem Erkennungsmuster auswählen.

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="Screenshot der Liste der ausgeschlossenen Erkennungsmuster.":::

        Vorfälle, die mit ausgeschlossenen Erkennungsmustern übereinstimmen, werden weiterhin ausgelöst, aber **nicht in Ihrer Warteschlange für aktive Vorfälle angezeigt**. Sie werden automatisch mit den folgenden Werten gefüllt:

        - **Status**: "Geschlossen"
        
        - **Schlussklassifizierung**: "Unbestimmt"

        - **Kommentar**: "Automatisch geschlossen, Fusion-Erkennungsmuster ausgeschlossen"

        - **Tag**: "ExcludedFusionDetectionPattern" - Sie können dieses Tag abfragen, um alle Vorfälle anzuzeigen, die diesem Erkennungsmuster entsprechen.

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="Screenshot von automatisch geschlossenem, ausgeschlossenem Fusion-Vorfall.":::



> [!NOTE]
> Microsoft Sentinel verwendet derzeit Verlaufsdaten von 30 Tagen, um die Machine Learning-Systeme zu trainieren. Diese Daten werden immer mit den Schlüsseln von Microsoft verschlüsselt, wenn sie die Pipeline für maschinelles Lernen durchlaufen. Die Trainingsdaten werden jedoch nicht mit [vom Kunden verwalteten Schlüsseln (Customer Managed Keys, CMK)](customer-managed-keys.md) verschlüsselt, wenn Sie CMK in Ihrem Microsoft Sentinel-Arbeitsbereich aktiviert haben. Um Fusion zu deaktivieren, navigieren Sie zu **Microsoft Sentinel** \> **Konfiguration** \> **Analytics \> Aktive Regeln**, klicken Sie mit der rechten Maustaste auf die Regel **Advanced Multistage Attack Detection** („Erweiterte mehrstufige Angriffserkennung“), und wählen Sie **Deaktivieren** aus.

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Konfiguration geplanter Analyseregeln für Fusion-Erkennungen

> [!IMPORTANT]
>
> - Die auf Fusion basierende Erkennung mithilfe von Analyseregelwarnungen befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

**Fusion** kann szenariobasierte mehrstufige Angriffe und neu auftretende Bedrohungen mithilfe von Warnungen erkennen, die von [Regeln für geplante Analysen](detect-threats-custom.md) generiert werden. Es wird empfohlen, zum Konfigurieren und Aktivieren dieser Regeln die folgenden Schritte auszuführen, damit Sie die Fusion-Funktionen von Microsoft Sentinel optimal nutzen können.

1. Fusion für neue Bedrohungen kann Warnungen verwenden, die von geplanten Analyseregeln generiert werden, sowohl [integriert](detect-threats-built-in.md#scheduled) als auch [erstellt von Ihren Sicherheitsanalysten](detect-threats-custom.md), die Kill-Chain-(Taktiken)- und Entitätszuordnungsinformationen enthalten. So stellen Sie sicher, dass die Ausgabe einer Analyseregel von Fusion verwendet werden kann, um aufkommende Bedrohungen zu erkennen:

    - Überprüfen Sie für diese geplanten Regeln die **Entitätszuordnung**. Im Konfigurationsbereich [Entitätszuordnung](map-data-fields-to-entities.md) können Sie Parameter aus den Abfrageergebnissen den von Microsoft Sentinel erkannten Entitäten zuordnen. Da Fusion Warnungen basierend auf Entitäten (wie *Benutzerkonto* oder *IP-Adresse*) korreliert, können seine ML-Algorithmen ohne die Entitätsinformationen keinen Warnungsabgleich durchführen.

    - Überprüfen Sie die **Taktiken** in den Details Ihrer Analyseregel. Der Fusion ML-Algorithmus verwendet [MITRE ATT&CK](https://attack.mitre.org/)-Taktikinformationen, um mehrstufige Angriffe zu erkennen, und die Taktiken, mit denen Sie die Analyseregeln kennzeichnen, werden in den resultierenden Vorfällen angezeigt. Die Berechnungen von Fusion können beeinträchtigt werden, wenn bei eingehenden Warnungen Taktikinformationen fehlen.

1. Fusion kann auch szenariobasierte Bedrohungen mithilfe von Regeln erkennen, die auf den folgenden **Regelvorlagen für geplante Analysen** basieren, die auf der Registerkarte **Regelvorlagen** im Blatt **Analyse** zu finden sind. Um diese Erkennungen zu aktivieren, wählen Sie den Regelnamen in der Vorlagengalerie aus und klicken Sie im Detailbereich auf **Regel erstellen**.

    - [Cisco – Firewallblock, aber erfolgreiche Anmeldung bei Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml)
    - [Fortinet – Beaconmuster erkannt](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml)
    - [IP-Adresse mit mehreren fehlgeschlagenen Azure AD-Anmeldungen meldet sich erfolgreich bei Palo Alto VPN an](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml)
    - [Mehrere Kennwortzurücksetzungen durch Benutzer](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml)
    - [Seltene Anwendungseinwilligung](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml)
    - [SharePointFileOperation über zuvor unbekannte IP-Adressen](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml)
    - [Verdächtige Ressourcenbereitstellung](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml)

    > [!NOTE]
    > Für die Menge geplanter Analyseregeln, die von Fusion verwendet werden, sucht der ML-Algorithmus Fuzzyübereinstimmungen für die KQL-Abfragen in den Vorlagen. Das Umbenennen der Vorlagen wirkt sich nicht auf Fusion-Erkennungen aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Erweiterte Erkennung von mehrstufigen Angriffen in Azure Sentinel](fusion.md).

Erfahren Sie mehr über die vielen [szenariobasierten Fusion-Erkennungen](fusion-scenario-reference.md).

Nachdem Sie nun mehr über die erweiterte Erkennung von mehrstufigen Angriffen erfahren haben, ist für Sie ggf. die folgende Schnellstartanleitung interessant. Darin wird veranschaulicht, wie Sie Einblicke in Ihre Daten und potenzielle Bedrohungen erhalten: [Erste Schritte mit Microsoft Sentinel](get-visibility.md).

Wenn Sie bereit sind, die Incidents zu untersuchen, die für Sie erstellt werden, lesen Sie das folgende Tutorial: [Untersuchen von Vorfällen mit Azure Sentinel](investigate-cases.md).
