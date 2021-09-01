---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 4c16fc8a6f497a05294a9b7357b8ffc4dab285f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801863"
---
#### <a name="built-in-log-streaming"></a>Integriertes Protokollstreaming

Verwenden Sie den[`func azure functionapp logstream`-Befehl](../articles/azure-functions/functions-core-tools-reference.md#func-azure-functionapp-list-functions), um den Empfang von Streamingprotokollen einer bestimmten in Azure ausgeführten Funktions-App wie im folgenden Beispiel gezeigt zu starten:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>Integriertes Protokollstreaming ist in Core Tools für Funktions-Apps noch nicht aktiviert, die unter Linux in einem Verbrauchsplan ausgeführt werden. Für diese Hostingpläne müssen Sie stattdessen Live Metrics Stream verwenden, um die Protokolle nahezu in Echtzeit anzuzeigen.

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Sie können auch [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) für Ihre Funktions-App in einem neuen Browserfenster anzeigen, indem Sie die Option `--browser` wie im folgenden Beispiel einschließen:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
