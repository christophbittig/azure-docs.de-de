---
title: Verbinden von AWS CloudTrail mit Microsoft Sentinel | Microsoft-Dokumentation
description: Verwenden Sie den AWS-Connector, um den Zugriff von Microsoft Sentinel auf AWS-Ressourcenprotokolle zu delegieren und eine Vertrauensstellung zwischen AWS CloudTrail und Microsoft Sentinel herzustellen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3475ac35692f793a40380993640069e42070883c
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517062"
---
# <a name="connect-aws-cloudtrail-to-microsoft-sentinel"></a>Verbinden von AWS CloudTrail mit Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Verwenden Sie den AWS-Connector, um Ihre AWS CloudTrail-Verwaltungsereignisse in Microsoft Sentinel zu streamen. Dieser Verbindungsprozess delegiert den Zugriff für Microsoft Sentinel auf Ihre AWS-Ressourcenprotokolle und schafft so eine Vertrauensbeziehung zwischen AWS CloudTrail und Microsoft Sentinel. Dies erfolgt in AWS durch Erstellen einer Rolle, die Microsoft Sentinel die Berechtigung erteilt, auf Ihre AWS-Protokolle zuzugreifen.

> [!NOTE]
> AWS CloudTrail weist [integrierte Beschränkungen](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) in seiner LookupEvents-API auf. Es sind nicht mehr als zwei Transaktionen pro Sekunde (TPS) pro Konto zulässig, und jede Abfrage kann maximal 50 Datensätze zurückgeben. Wenn also ein einziger Mandant ständig mehr als 100 Datensätze pro Sekunde in einer Region generiert, kommen es zu Backlogs und Verzögerungen bei der Datenerfassung.
> Derzeit können Sie AWS Commercial CloudTrail nur mit Microsoft Sentinel verbinden, nicht mit AWS GovCloud CloudTrail.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Schreibzugriff auf den Microsoft Sentinel-Arbeitsbereich.

> [!NOTE]
> Microsoft Sentinel sammelt CloudTrail-Verwaltungsereignisse aus allen Regionen. Es wird davon abgeraten, Ereignisse aus einer Region in eine andere zu streamen.

## <a name="connect-aws"></a>Herstellen einer Verbindung mit AWS 


1. Wählen Sie in Microsoft Sentinel **Datenconnectors** und dann in der Tabelle die Zeile **Amazon Web Services** aus und wählen Sie im Bereich rechts **Open connector page**.

1. Befolgen Sie die Anleitungen unter **Konfiguration**, und führen Sie die folgenden Schritte aus.
 
1.  Wählen Sie in Ihrer Amazon Web Services-Konsole unter **Sicherheit, Identität und Compliance** den Eintrag **IAM** aus.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Wählen Sie **Rollen** und dann **Rolle erstellen** aus.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Wählen Sie **Anderes AWS-Konto** aus. Geben Sie im Feld **Konto-ID** die **Microsoft-Konto-ID** (**123412341234**) ein, die Sie auf der AWS Connector-Seite im Microsoft Sentinel-Portal finden.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Vergewissern Sie sich, dass **Externe ID erforderlich** ausgewählt ist, und geben Sie dann die externe ID (Arbeitsbereich-ID) ein, die Sie auf der AWS Connector-Seite im Microsoft Sentinel-Portal finden.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Wählen Sie unter **Berechtigungsrichtlinie anfügen** **AWSCloudTrailReadOnlyAccess** aus.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Geben Sie ein Tag (optional) ein.

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Geben Sie anschließend einen Wert in **Rollenname** ein, und wählen Sie die Schaltfläche **Rolle erstellen** aus.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  Wählen Sie in der Liste der Rollen die Rolle aus, die Sie erstellt haben.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Kopieren Sie den Wert **Rollen-ARN**. Fügen Sie ihn im Microsoft Sentinel-Portal auf dem Bildschirm des Amazon Web Services Connectors in das Feld **Rolle hinzuzufügen** ein und wählen Sie **Hinzufügen**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Um das relevante Schema in Log Analytics für die AWS-Ereignisse zu verwenden, suchen Sie nach **AWSCloudTrail**.

    > [!IMPORTANT]
    > Mit Stand vom 1. Dezember 2020 wurde das Feld **AwsRequestId** durch das Feld **AwsRequestId_** ersetzt (beachten Sie den hinzugefügten Unterstrich). Die Daten im alten Feld **AwsRequestId** werden bis zum Ende des vom Kunden angegebenen Aufbewahrungszeitraums beibehalten.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument wurde beschrieben, wie Sie AWS CloudTrail mit Microsoft Sentinel verbinden. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
