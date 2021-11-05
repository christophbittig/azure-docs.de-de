---
title: Warnungsüberprüfung in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie überprüfen können, ob Ihre Sicherheitswarnungen in Microsoft Defender für Cloud ordnungsgemäß konfiguriert sind.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0070bf9a071de8108fff21e0a1816c9d73f9a226
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096081"
---
# <a name="alert-validation-in-microsoft-defender-for-cloud"></a>Warnungsüberprüfung in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Dokument erfahren Sie, wie Sie überprüfen, ob Ihr System ordnungsgemäß für Microsoft Defender für Cloud-Warnungen konfiguriert ist.

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Warnungen sind die Benachrichtigungen, die Defender für Cloud generiert, wenn Bedrohungen für Ihre Ressourcen erkannt werden. Security Center priorisiert die Warnungen und listet sie zusammen mit den Informationen auf, die erforderlich sind, um das Problem schnell zu untersuchen. Defender für Cloud stellt außerdem Empfehlungen zur Abwehr eines Angriffs bereit.
Weitere Informationen finden Sie unter [Sicherheitswarnungen in Defender für Cloud](alerts-overview.md) und [Verwalten von und Reagieren auf Sicherheitswarnungen](managing-and-responding-alerts.md).


## <a name="generate-sample-security-alerts"></a>Generieren von Beispielsicherheitswarnungen

Wenn Sie die neuen Vorschaufunktionen für Warnungen verwenden, die unter [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md) beschrieben werden, können Sie Beispielwarnungen erstellen. Dafür sind nur einige wenige Klicks im Azure-Portal auf der Seite „Sicherheitswarnungen“ erforderlich.

Verwenden Sie Beispielwarnungen für Folgendes:

- Bemessen des Werts und der Möglichkeiten Ihrer Microsoft Defender-Pläne
- Überprüfen von Konfigurationen, die Sie für Ihre Sicherheitswarnungen vorgenommen haben (z. B. SIEM-Integrationen, Workflowautomatisierung und E-Mail-Benachrichtigungen)

So erstellen Sie Beispielwarnungen:

1. Wählen Sie als Benutzer mit der Rolle **Abonnementmitwirkender** auf der Symbolleiste auf der Seite „W **Beispielwarnungen erstellen** aus.
1. Wählen Sie das Abonnement aus.
1. Wählen Sie den entsprechenden Microsoft Defender-Plan aus, für den Sie Warnungen anzeigen möchten. 
1. Klicken Sie auf **Beispielwarnungen erstellen**.

    :::image type="content" source="media/alert-validation/create-sample-alerts-procedures.png" alt-text="Schritte zum Erstellen von Beispielwarnungen in Microsoft Defender für Cloud":::
    
    In einer Benachrichtigung werden Sie darüber informiert, dass die Beispielwarnungen erstellt werden:

    :::image type="content" source="media/alert-validation/notification-sample-alerts-creation.png" alt-text="Benachrichtigung, dass die Beispielwarnungen generiert werden":::

    Nach einigen Minuten werden die Warnungen auf der Seite mit Sicherheitswarnungen angezeigt. Sie werden auch an anderen Stellen angezeigt, die Sie für den Empfang von Microsoft Defender für Cloud-Sicherheitswarnungen konfiguriert haben (verbundene SIEM-Lösungen, E-Mail-Benachrichtigungen usw.).

    :::image type="content" source="media/alert-validation/sample-alerts.png" alt-text="Beispielwarnungen in der Liste der Sicherheitswarnungen.":::

    > [!TIP]
    > Die Warnungen gelten für simulierte Ressourcen.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulieren von Warnungen auf Ihren Azure-VMs (Windows) <a name="validate-windows"></a>

Nach der Installation des Log Analytics-Agents auf Ihrem Computer führen Sie auf dem Computer, auf dem sich die angegriffene Ressource für die Warnung befinden soll, die folgenden Schritte aus:

1. Kopieren Sie eine ausführbare Datei (beispielsweise **calc.exe**) auf den Desktop des Computers oder in ein beliebiges anderes Verzeichnis, und benennen Sie sie in **ASC_AlertTest_662jfi039N.exe** um.
1. Öffnen Sie die Eingabeaufforderung, und führen Sie die Datei mit einem Argument (beliebiger Pseudoname) aus. Beispiel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Warten Sie fünf bis zehn Minuten, und öffnen Sie die Defender für Cloud-Warnungen. Eine Warnung sollte angezeigt werden.

> [!NOTE]
> Vergewissern Sie sich bei der Prüfung dieser Testwarnung für Windows, dass **Arguments Auditing Enabled** (Argumentüberprüfung aktiviert) auf **TRUE** festgelegt ist. Sollte die Option auf **FALSE** festgelegt sein, müssen Sie die Überprüfung von Befehlszeilenargumenten aktivieren. Verwenden Sie den folgenden Befehl, um die Einstellung zu aktivieren:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulieren von Warnungen auf Ihren Azure-VMs (Linux) <a name="validate-linux"></a>

Nach der Installation des Log Analytics-Agents auf Ihrem Computer führen Sie auf dem Computer, auf dem sich die angegriffene Ressource für die Warnung befinden soll, die folgenden Schritte aus:

1. Kopieren Sie eine ausführbare Datei an einen geeigneten Speicherort, und benennen Sie sie in `./asc_alerttest_662jfi039n` um. Beispiel:

    `cp /bin/echo ./asc_alerttest_662jfi039n`

1. Öffnen Sie die Eingabeaufforderung, und führen Sie diese Datei aus:

    `./asc_alerttest_662jfi039n testing eicar pipe`

1. Warten Sie fünf bis zehn Minuten, und öffnen Sie dann die Defender für Cloud-Warnungen. Eine Warnung sollte angezeigt werden.

## <a name="simulate-alerts-on-kubernetes"></a>Simulieren von Warnung in Kubernetes <a name="validate-kubernetes"></a>

Wenn Sie Azure Kubernetes Service in Defender für Cloud integriert haben, können Sie mit dem folgenden kubectl-Befehl testen, ob Ihre Warnungen funktionieren:

`kubectl get pods --namespace=asc-alerttest-662jfi039n`

Weitere Informationen zum Schützen Ihrer Kubernetes-Knoten und -Cluster finden Sie unter [Einführung in Microsoft Defender für Kubernetes](defender-for-kubernetes-introduction.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurden Sie in den Prozess der Warnungsüberprüfung eingeführt. Nachdem Sie sich mit dieser Überprüfung vertraut gemacht haben, können Sie mit den folgenden Artikeln fortfahren:

* [Überprüfen der Azure Key Vault-Bedrohungserkennung in Microsoft Defender für Cloud](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md): Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und auf Sicherheitsincidents in Defender für Cloud reagieren.
* [Verstehen der Sicherheitswarnungen in Microsoft Defender für Cloud](./alerts-overview.md): Hier finden Sie Informationen zu den unterschiedlichen Arten von Sicherheitswarnungen.
