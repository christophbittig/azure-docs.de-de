---
title: Warnungsüberprüfung in Azure Security Center | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie überprüfen können, ob Ihre Sicherheitswarnungen in Azure Security Center ordnungsgemäß konfiguriert sind.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: 48a695f4217c7300c4d7db6edc23452ad4eaa55f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631702"
---
# <a name="alert-validation-in-azure-security-center"></a>Warnungsüberprüfung in Azure Security Center
In diesem Dokument erfahren Sie, wie Sie überprüfen, ob Ihr System ordnungsgemäß für Azure Security Center-Warnungen konfiguriert ist.

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Warnungen sind die Benachrichtigungen, die Security Center generiert, wenn Bedrohungen für Ihre Ressourcen erkannt werden. Security Center priorisiert die Warnungen und listet sie zusammen mit den Informationen auf, die erforderlich sind, um das Problem schnell zu untersuchen. Security Center stellt außerdem Empfehlungen zur Reaktion auf einen Angriff bereit.
Weitere Informationen finden Sie unter [Sicherheitswarnungen in Azure Security Center](security-center-alerts-overview.md) und [Verwalten von und Reagieren auf Sicherheitswarnungen](security-center-managing-and-responding-alerts.md).


## <a name="generate-sample-azure-defender-alerts"></a>Generieren von Azure Defender-Beispielwarnungen

Wenn Sie die neuen Vorschaufunktionen für Warnungen verwenden, die unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md) beschrieben werden, können Sie Beispielwarnungen erstellen. Dafür sind nur einige wenige Klicks im Azure-Portal auf der Seite „Sicherheitswarnungen“ erforderlich.

Verwenden Sie Beispielwarnungen für Folgendes:

- Evaluieren des Werts und der Möglichkeiten von Azure Defender
- Überprüfen von Konfigurationen, die Sie für Ihre Sicherheitswarnungen vorgenommen haben (z. B. SIEM-Integrationen, Workflowautomatisierung und E-Mail-Benachrichtigungen)

So erstellen Sie Beispielwarnungen:

1. Wählen Sie als Benutzer mit der Rolle **Abonnementmitwirkender** auf der Symbolleiste auf der Seite „W **Beispielwarnungen erstellen** aus.
1. Wählen Sie das Abonnement aus.
1. Wählen Sie den entsprechenden Azure Defender-Plan aus, für den Sie Warnungen anzeigen möchten. 
1. Klicken Sie auf **Beispielwarnungen erstellen**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Schritte zum Erstellen von Warnungen in Azure Security Center.":::
    
    In einer Benachrichtigung werden Sie darüber informiert, dass die Beispielwarnungen erstellt werden:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Benachrichtigung, dass die Beispielwarnungen generiert werden":::

    Nach einigen Minuten werden die Warnungen auf der Seite mit Sicherheitswarnungen angezeigt. Sie werden auch an anderen Stellen angezeigt, die Sie für den Empfang von Azure Security Center-Sicherheitswarnungen konfiguriert haben (verbundene SIEM-Lösungen, E-Mail-Benachrichtigungen usw.).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Beispielwarnungen in der Liste der Sicherheitswarnungen.":::

    > [!TIP]
    > Die Warnungen gelten für simulierte Ressourcen.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulieren von Warnungen auf Ihren Azure-VMs (Windows) <a name="validate-windows"></a>

Nach der Installation des Security Center-Agents auf Ihrem Computer führen Sie auf dem Computer, auf dem sich die angegriffene Ressource für die Warnung befinden soll, die folgenden Schritte aus:

1. Kopieren Sie eine ausführbare Datei (beispielsweise **calc.exe**) auf den Desktop des Computers oder in ein beliebiges anderes Verzeichnis, und benennen Sie sie in **ASC_AlertTest_662jfi039N.exe** um.
1. Öffnen Sie die Eingabeaufforderung, und führen Sie die Datei mit einem Argument (beliebiger Pseudoname) aus. Beispiel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Warten Sie fünf bis zehn Minuten, und öffnen Sie die Security Center-Warnungen. Eine Warnung sollte angezeigt werden.

> [!NOTE]
> Vergewissern Sie sich bei der Prüfung dieser Testwarnung für Windows, dass **Arguments Auditing Enabled** (Argumentüberprüfung aktiviert) auf **TRUE** festgelegt ist. Sollte die Option auf **FALSE** festgelegt sein, müssen Sie die Überprüfung von Befehlszeilenargumenten aktivieren. Verwenden Sie den folgenden Befehl, um die Einstellung zu aktivieren:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulieren von Warnungen auf Ihren Azure-VMs (Linux) <a name="validate-linux"></a>

Nach der Installation des Security Center-Agents auf Ihrem Computer führen Sie auf dem Computer, auf dem sich die angegriffene Ressource für die Warnung befinden soll, die folgenden Schritte aus:

1. Kopieren Sie eine ausführbare Datei an einen geeigneten Speicherort, und benennen Sie sie in `./asc_alerttest_662jfi039n` um. Beispiel:

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. Öffnen Sie die Eingabeaufforderung, und führen Sie diese Datei aus:

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. Warten Sie fünf bis zehn Minuten, und öffnen Sie die Security Center-Warnungen. Eine Warnung sollte angezeigt werden.

## <a name="simulate-alerts-on-kubernetes"></a>Simulieren von Warnung in Kubernetes <a name="validate-kubernetes"></a>

Wenn Sie Azure Kubernetes Service in Security Center integriert haben, testen Sie mit dem folgenden kubectl-Befehl, ob Ihre Warnungen funktionieren:

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Weitere Informationen zum Schützen Ihrer Kubernetes-Knoten und -Cluster finden Sie unter [Einführung in Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden Sie in den Prozess der Warnungsüberprüfung eingeführt. Nachdem Sie sich mit dieser Überprüfung vertraut gemacht haben, können Sie mit den folgenden Artikeln fortfahren:

* [Überprüfen der Azure Key Vault-Bedrohungserkennung in Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md): Erfahren Sie, wie Sie Warnungen verwalten und auf Sicherheitsvorfälle in Security Center reagieren.
* [Verstehen der Sicherheitswarnungen in Azure Security Center](./security-center-alerts-overview.md): Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
