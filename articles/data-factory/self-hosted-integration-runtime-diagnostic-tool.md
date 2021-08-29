---
title: Diagnosetool für die selbstgehostete Integration Runtime
description: Diagnosetool für die selbstgehostete Integration Runtime
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
- devx-track-azurepowershell
ms.date: 07/28/2021
ms.openlocfilehash: b8aa070759ea29c9c2853d0c8a6af184bc169569
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347065"
---
# <a name="diagnostic-tool-for-self-hosted-integration-runtime"></a>Diagnosetool für die selbstgehostete Integration Runtime
Die selbstgehostete Integration Runtime ist die Compute-Infrastruktur, mit der Azure Data Factory Datenintegrationsfunktionen für verschiedene Netzwerkumgebungen bereitstellt. Für die Installation einer selbstgehosteten Integration Runtime ist ein lokaler Computer oder ein virtueller Computer in einem privaten Netzwerk erforderlich. Manchmal ist es schwierig, Probleme auf lokalen Computern zu untersuchen, z. B. Probleme im Zusammenhang mit Netzwerk, Firewall, Abhängigkeit oder Betriebssystem. In diesem Artikel wird ein neues Diagnosetool für die Problembehandlung in lokalen Umgebungen beschrieben.

Das Tool führt eine Reihe von Testszenarien auf dem Computer mit der selbstgehosteten Integration Runtime aus. Bei jedem Szenario gibt es typische Integritätsprüfungsfälle für allgemeine Probleme. Kunden können beim Auftreten von Problemen das Feature „Problembehandlung“ auslösen. Das Tool sammelt die Informationen zur Kundenumgebung und führt die Integritätsprüfungsfälle aus. 

## <a name="get-started"></a>Erste Schritte 
Es gibt zwei Möglichkeiten zum Ausführen des Diagnosetools, um mögliche Probleme zu erkennen:

- Wenn Sie eine selbstgehostete Integration Runtime auf einem lokalen Computer installieren, können Sie über den Configuration Manager auf das Problembehandlungsfeature zugreifen. Falls Probleme auftreten, wählen Sie **Problembehandlung** aus, um das Diagnosetool auszuführen. Sie können diese Option auch nach der Installation auf der Registerkarte **Diagnose** auswählen.

   :::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/troubleshoot-ui.png" alt-text="Screenshot, der zeigt, wie Probleme mithilfe des Configuration Manager für Runtime behoben werden":::
   
- Sie können das Diagnosetool auch über die Befehlszeile starten:

   ```console
   dmgcmd.exe -ts [AUTH_KEY]
   ```

## <a name="diagnostic-result"></a>Diagnoseergebnis
Die Ausführungsergebnis und Detailprotokollmeldungen werden als HTML-Bericht generiert. Sie können den Fehler überprüfen und die vorgeschlagenen Entschärfungsmethoden oder die URL für öffentliche Dokumente aus dem Bericht abrufen.

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-process.png" alt-text="Screenshot des Diagnoseprozesses":::

:::image type="content" source="./media/self-hosted-integration-runtime-diagnostic-tool/diagnostic-report.png" alt-text="Screenshot des Berichts mit dem Diagnoseergebnis":::

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Integration Runtime-Konzepte in Azure Data Factory](./concepts-integration-runtime.md).

- Informieren Sie sich über das [Erstellen einer selbstgehosteten Integration Runtime im Azure-Portal](./create-self-hosted-integration-runtime.md).
