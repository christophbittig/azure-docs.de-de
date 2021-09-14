---
title: Kopieren, Sichern und Verschieben Ihrer Azure Stream Analytics-Aufträge zwischen Regionen
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Stream Analytics-Auftrag verschieben, kopieren und sichern.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/11/2019
ms.openlocfilehash: 7b3d203d3d34612b3495fa3eceaf13e357eaa2be
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829406"
---
# <a name="copy-back-up-and-move-your-azure-stream-analytics-jobs-between-regions"></a>Kopieren, Sichern und Verschieben Ihrer Azure Stream Analytics-Aufträge zwischen Regionen

Sie können Ihre bereitgestellten Azure Stream Analytics-Aufträge mit Visual Studio Code oder Visual Studio kopieren oder sichern. Beim Kopieren eines Auftrags in eine andere Region wird der letzte Ausgabezeitpunkt nicht mitkopiert. Daher können Sie beim Starten des kopierten Auftrags die Option [**Zeitpunkt der letzten Beendigung**](./start-job.md#start-options) nicht verwenden.

## <a name="before-you-begin"></a>Voraussetzungen
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

* Installieren Sie die [Azure Stream Analytics-Erweiterung für Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) oder die [Azure Stream Analytics-Tools für Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klicken Sie auf der Aktivitätsleiste von Visual Studio Code auf das **Azure**-Symbol, und erweitern Sie dann den Knoten **Stream Analytics**. Ihre Aufträge sollten unter Ihren Abonnements angezeigt werden.

   ![Öffnen von Stream Analytics-Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Suchen Sie den zu exportierenden Auftrag im **Stream Analytics-Explorer** in Visual Studio Code, um einen Auftrag in ein lokales Projekt zu exportieren. Wählen Sie dann einen Ordner für Ihr Projekt aus.

    ![Suchen von ASA-Aufträgen in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Das Projekt wird in den von Ihnen ausgewählten Ordner exportiert und zu Ihrem aktuellen Arbeitsbereich hinzugefügt.

3. Um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem anderen Namen zu sichern, wählen Sie **Zum Veröffentlichen aus Ihren Abonnements auswählen** im Abfrage-Editor (\*.asaql) aus, und befolgen Sie die Anweisungen.

    ![Veröffentlichen in Azure in Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Befolgen Sie die [Anweisungen zum Exportieren eines bereitgestellten Azure Stream Analytics-Auftrags zu einem Projekt](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Öffnen Sie im Abfrage-Editor die \*.asaql-Datei, wählen Sie **An Azure übermitteln** im Skript-Editor aus, und befolgen Sie die Anweisungen, um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem neuen Namen zu sichern.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-visual-studio-code.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)