---
title: Verbinden von Sicherheitswarnungen für Microsoft Sentinel
description: Erfahren Sie, wie Sie Sicherheitswarnungen aus Microsoft Defender für Cloud verbinden und an Microsoft Sentinel streamen.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 33d43aa25a8e51c0330c2eaba213ef8d5644e62f
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522116"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Sicherheitswarnungen von Microsoft Defender for Cloud verbinden

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>Hintergrund

[Microsoft Defender for Cloud](../security-center/azure-defender.md), die integrierte Cloud-Workloadschutz-Plattform (Cloud Workload Protection Plattform, CWPP) von [Defender für Cloud,](../security-center/security-center-introduction.md)ist ein Sicherheitsverwaltungstool, mit dem Sie Bedrohungen in Hybrid Cloud-Workloads erkennen und schnell darauf reagieren können.

Mit diesem Connector können Sie Sicherheitswarnungen aus Defender für Cloud an Microsoft Sentinel streamen, damit Sie Sicherheitswarnungen und die von ihnen generierten Vorfälle in einem umfassenderen Bedrohungskontext der Organisation anzeigen, analysieren und darauf reagieren können.

Da Defender für Cloud selbst pro Abonnement aktiviert wird, wird auch der Defender für Cloud-Connector für jedes Abonnement separat aktiviert oder deaktiviert.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Warnungssynchronisierung

- Wenn Sie Microsoft Defender für Cloud mit Microsoft Sentinel verbinden, wird der Status von Sicherheitswarnungen, die in Microsoft Sentinel erfasst werden, zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in Defender for Cloud geschlossen wird, wird diese Warnung auch in Microsoft Sentinel als geschlossen angezeigt.

- Die Änderung des Status einer Warnung in Defender for Cloud wirkt sich *nicht* auf den Status aller Microsoft Sentinel **Vorfälle** aus, die die Microsoft Sentinel-Warnung enthalten, sondern nur auf den der Warnung selbst.

### <a name="bi-directional-alert-synchronization"></a>Bidirektionale Synchronisierung von Warnungen

- Durch die Aktivierung der **bi-direktionalen Synchronisierung** wird der Status der ursprünglichen Sicherheitswarnungen automatisch mit dem der Microsoft Sentinel-Vorfälle, die diese Warnmeldungen enthalten, synchronisiert. Wenn beispielsweise ein Microsoft Sentinel-Vorfall, der eine Sicherheitswarnung enthält, geschlossen wird, wird die entsprechende ursprüngliche Warnung in Microsoft Defender for Cloud automatisch geschlossen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Microsoft Sentinel-Arbeitsbereich.

- Sie müssen in den Abonnements, deren Protokolle Sie streamen, über die Rolle „Sicherheitsleseberechtigter“ verfügen.

- Sie müssen mindestens einen Plan in Microsoft Defender for Cloud für jedes Abonnement aktivieren, für das Sie den Connector aktivieren möchten. Um Microsoft Defender-Pläne für ein Abonnement zu aktivieren, benötigen Sie die **Rolle des Sicherheitsadministrators**  für dieses Abonnement.

- Zum Aktivieren der bidirektionalen Synchronisierung müssen Sie über die Rolle **Mitwirkender** oder **Sicherheitsadministrator** für das betreffende Abonnement verfügen.

## <a name="connect-to-microsoft-defender-for-cloud"></a>Verbinden zu Microsoft Defender für Cloud

1. Wählen Sie in Microsoft Sentinel **im Navigationsmenü** die Option Daten-Konnektors aus.

1. Wählen Sie im Katalog des Daten-Konnektors **Microsoft Defender für Cloud** und dann im Detailbereich **Konnektor-Seite öffnen** aus.

1. Unter **Konfiguration** wird eine Liste der Abonnements in Ihrem Mandanten und der Status der Verbindung mit Microsoft Defender für Cloud angezeigt. Wählen Sie die Umschaltfläche **Status** neben jedem Abonnement aus, dessen Warnungen Sie an Microsoft Sentinel streamen möchten. Wenn Sie eine Verbindung mit mehreren Abonnements auf einmal herstellen möchten, aktivieren Sie die Kontrollkästchen neben den relevanten Abonnements, und klicken Sie dann auf der Leiste über der Liste auf die Schaltfläche **Verbinden**.

    > [!NOTE]
    > - Die Kontrollkästchen und die Umschaltflächen **Verbinden** sind nur für die Abonnements aktiviert, für die Sie über die erforderlichen Berechtigungen verfügen.
    > - Die Schaltfläche **Verbinden** ist nur aktiviert, wenn das Kontrollkästchen für mindestens ein Abonnement aktiviert wurde.

1. Um die bidirektionale Synchronisierung für ein Abonnement zu aktivieren, suchen Sie das Abonnement in der Liste, und wählen Sie **aktiviert** in der Dropdownliste in der Spalte **Bidirektionale Synchronisierung** aus. Um die bidirektionale Synchronisierung für mehrere Abonnements auf einmal zu aktivieren, aktivieren Sie die entsprechenden Kontrollkästchen, und klicken Sie auf der Leiste über der Liste auf die Schaltfläche **Enable bi-directional sync** (Bidirektionale Synchronisierung aktivieren).

    > [!NOTE]
    > - Die Kontrollkästchen und Dropdownlisten sind nur für die Abonnements aktiviert, für die Sie über die [erforderlichen Berechtigungen](#prerequisites) verfügen.
    > - Die Schaltfläche **Enable bi-directional sync** (Bidirektionale Synchronisierung aktivieren) ist nur aktiviert, wenn das Kontrollkästchen für mindestens ein Abonnement aktiviert wurde.

1. In der Spalte **Microsoft Defender-Pläne** der Liste können Sie sehen, ob Microsoft Defender-Pläne für Ihr Abonnement aktiviert sind (eine Voraussetzung für die Aktivierung des Konnektors). Der Wert für jedes Abonnement in dieser Spalte ist entweder leer (d. h., dass keine Defender-Pläne aktiviert sind) oder lautet „All enabled“ (Alle aktiviert) oder „Some enabled“ (Einige aktiviert). Für Abonnements mit dem Wert „Some enabled“ (Einige aktiviert) ist der Link **Alle aktivieren** vorhanden. Über diesen gelangen Sie zum Microsoft Defender-Konfigurations-Dashboard für das Abonnement, in dem Sie zu aktivierende Defender-Pläne auswählen können. Über die Link-Schaltfläche **Microsoft Defender für alle Abonnements aktivieren** auf der Leiste über der Liste gelangen Sie zu Ihrer Seite Microsoft Defender für Cloud Erste Schritte, auf der Sie auswählen können, für welche Abonnements Microsoft Defender für Cloud vollständig aktiviert werden soll.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Screenshot der Konfiguration des Microsoft Defender für Cloud-Konnektors":::

1. Sie können auswählen, ob die Warnungen von Microsoft Defender für Cloud automatisch Vorfälle in Microsoft Sentinel generieren sollen. Wählen Sie unter **Incidents erstellen** die Option **Aktiviert** aus, um die Standardanalyseregel zu aktivieren, durch die [aus Warnungen automatisch Incidents erstellt werden](create-incidents-from-alerts.md). Anschließend können Sie diese Regel unter **Analytics** auf der Registerkarte **Aktive Regeln** bearbeiten.

    > [!TIP]
    > Berücksichtigen Sie beim Konfigurieren [von benutzerdefinierten Analyseregeln](detect-threats-custom.md) für Warnungen von Microsoft Defender für Cloud den Warnungsschweregrad, um das Öffnen von Vorfällen für Informationswarnungen zu vermeiden. 
    >
    > Informationswarnungen in Microsoft Defender for Cloud stellen selbst kein Sicherheitsrisiko dar und sind nur im Kontext eines vorhandenen, offenen Vorfalls relevant. Weitere Informationen finden Sie unter [Sicherheitswarnungen und Vorfälle in Microsoft Defender for Cloud.](../security-center/security-center-alerts-overview.md)
    > 
    

## <a name="find-and-analyze-your-data"></a>Suchen und Analysieren Ihrer Daten

> [!NOTE]
> Die *bidirektionale* Synchronisierung von Warnungen kann einige Minuten in Anspruch nehmen. Änderungen des Status von Warnungen werden eventuell nicht sofort angezeigt.

- Sicherheitswarnungen werden in der *Tabelle SecurityAlert* in Ihrem Log Analytics-Arbeitsbereich gespeichert.

- Um Sicherheitswarnungen in Log Analytics abzufragen, kopieren Sie Folgendes als Ausgangspunkt in Ihr Abfragefenster:

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Auf der Registerkarte **Nächste Schritte** auf der Connectorseite finden Sie weitere nützliche Beispielabfragen, Analyseregelvorlagen und empfohlene Arbeitsmappen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Microsoft Defender for Cloud mit Microsoft Sentinel verbinden und Warnungen zwischen ihnen synchronisieren können. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Weitere Informationen zu [Microsoft Sentinel finden](detect-threats-built-in.md) Sie in den folgenden Artikeln:
- Schreiben Sie eigene Regeln, um [Bedrohungen zu erkennen](detect-threats-custom.md).
