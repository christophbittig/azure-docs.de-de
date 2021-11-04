---
title: Verhindern von Fehlkonfigurationen mit Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie auf Seiten mit Empfehlungsdetails die Optionen „Erzwingen“ und „Ablehnen“ von Defender für Cloud verwenden.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 08/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 772055f91c10a4a2b58945ee314271e512be61bb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131037438"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Sicherheitsbezogene Fehlkonfigurationen sind eine häufige Ursache für Sicherheitsincidents. Defender für Cloud hilft jetzt Fehlkonfigurationen neuer Ressourcen im Hinblick auf bestimmte Empfehlungen zu *verhindern*. 

Dieses Feature kann dazu beitragen, dass Ihre Workloads geschützt sind und Ihre Sicherheitsbewertung stabil bleibt.

Für die Erzwingung einer sicheren Konfiguration basierend auf einer bestimmten Empfehlung gibt es zwei Modi:

- Mit dem Effekt **Deny** (Ablehnen) von Azure Policy können Sie verhindern, dass fehlerhafte Ressourcen erstellt werden.
- Mit der Option **Enforce** (Erzwingen) können Sie die Auswirkung **DeployIfNotExist** von Azure Policy nutzen und nicht konforme Ressourcen bei der Erstellung automatisch korrigieren.

Sie finden diese Option oben auf der Seite mit den Ressourcendetails für ausgewählte Sicherheitsempfehlungen (siehe [Empfehlungen mit der Option „Ablehnen/Erzwingen“](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Verhindern der Ressourcenerstellung

1. Öffnen Sie die Empfehlung, deren Anforderungen von Ihren neuen Ressourcen erfüllt werden müssen, und wählen Sie oben auf der Seite die Schaltfläche **Ablehnen** aus.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-deny-button.png" alt-text="Empfehlungsseite mit hervorgehobener Schaltfläche „Ablehnen“.":::

    Der Konfigurationsbereich wird geöffnet, und die Bereichsoptionen werden aufgeführt. 

1. Legen Sie den Bereich fest, indem Sie das relevante Abonnement oder die Verwaltungsgruppe auswählen.

    > [!TIP]
    > Sie können die drei Punkte am Ende der Zeile verwenden, um ein Abonnement zu ändern, oder über die Kontrollkästchen mehrere Abonnements oder Gruppen und dann die Option **In „Ablehnen“ ändern** auswählen.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-prevent-resource-creation.png" alt-text="Festlegen des Bereichs für „Ablehnen“ von Azure Policy.":::


## <a name="enforce-a-secure-configuration"></a>Erzwingen einer sicheren Konfiguration

1. Öffnen Sie die Empfehlung, für die Sie eine Vorlagenbereitstellung durchführen möchten, falls die Anforderungen der Empfehlung von neuen Ressourcen nicht erfüllt werden. Wählen Sie anschließend oben auf der Seite die Schaltfläche **Erzwingen** aus.

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-button.png" alt-text="Empfehlungsseite mit hervorgehobener Schaltfläche „Erzwingen“.":::

    Der Konfigurationsbereich wird geöffnet, und alle Optionen der Richtlinienkonfiguration werden angezeigt. 

    :::image type="content" source="./media/implement-security-recommendations/recommendation-enforce-config.png" alt-text="Erzwingen von Konfigurationsoptionen.":::

1. Legen Sie den Bereich, den Zuweisungsnamen und andere relevante Optionen fest.

1. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="recommendations-with-denyenforce-options"></a>Empfehlungen mit Optionen zum Ablehnen/Erzwingen

Diese Empfehlungen können mit der Option **Ablehnen** verwendet werden:

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Diese Empfehlungen können mit der Option **Erzwingen** verwendet werden:

- Die Überwachung in SQL Server muss aktiviert werden.
- Für Azure Arc-fähige Kubernetes-Cluster muss die Azure Defender-Erweiterung installiert sein
- Azure Backup sollte für virtuelle Computer aktiviert sein
- Microsoft Defender für App Service muss aktiviert sein.
- Microsoft Defender für Containerregistrierungen muss aktiviert sein.
- Microsoft Defender für DNS muss aktiviert sein.
- Microsoft Defender für Key Vault muss aktiviert sein.
- Microsoft Defender für Kubernetes muss aktiviert sein
- Microsoft Defender für Resource Manager muss aktiviert sein.
- Microsoft Defender für Server muss aktiviert sein.
- Microsoft Defender für Azure SQL-Datenbank-Server muss aktiviert sein.
- Microsoft Defender für SQL-Server auf Computern muss aktiviert sein.
- Microsoft Defender für SQL muss für ungeschützte Azure SQL-Server aktiviert sein.
- Microsoft Defender für Storage muss aktiviert sein.
- Azure Policy-Add-On für Kubernetes muss auf Ihren Clustern installiert und aktiviert sein
- Diagnoseprotokolle in Azure Stream Analytics sollten aktiviert werden.
- Diagnoseprotokolle in Batch-Konten sollten aktiviert sein.
- In Data Lake Analytics sollten Diagnoseprotokolle aktiviert sein.
- In Event Hub sollten Diagnoseprotokolle aktiviert sein
- Diagnoseprotokolle in Key Vault sollten aktiviert sein.
- In Logic Apps müssen Diagnoseprotokolle aktiviert sein
- In den Suchdiensten müssen Diagnoseprotokolle aktiviert sein
- In Service Bus sollten Diagnoseprotokolle aktiviert sein.
