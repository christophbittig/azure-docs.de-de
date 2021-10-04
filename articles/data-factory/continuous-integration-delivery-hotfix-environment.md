---
title: Verwenden einer Hotfix-Produktionsumgebung
description: Erfahren Sie, wie Sie eine Hotfix-Produktionsumgebung mit Continuous Integration und Continuous Delivery in Azure Data Factory-Pipelines verwenden.
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788b576d351984c4f6a3dff7fd43056aa95aba3c
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219518"
---
# <a name="using-a-hotfix-production-environment"></a>Verwenden einer Hotfix-Produktionsumgebung

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wenn Sie eine Factory in der Produktionsumgebung bereitstellen und ein sofort zu behebender Fehler vorhanden ist, Sie den aktuellen Kollaborationsbranch aber nicht bereitstellen können, müssen Sie unter Umständen einen Hotfix verwenden. Dieser Ansatz wird als Quick Fix Engineering oder QFE bezeichnet.

## <a name="steps-to-deploy-a-hotfix"></a>Schritte zum Bereitstellen eines Hotfixes

Führen Sie die folgenden Schritte aus, um einen Hotfix in Ihrer Produktions- und Testumgebung bereitzustellen.

1.    Navigieren Sie in Azure DevOps zu dem Release, das für die Produktion bereitgestellt wurde. Suchen Sie nach dem letzten bereitgestellten Commit.

1.    Rufen Sie aus der Commitmeldung die Commit-ID des Kollaborationsbranch ab.

1.    Erstellen Sie aus diesem Commit einen neuen Hotfixbranch.

1.    Navigieren Sie zu Azure Data Factory Studio und dort zum Hotfixbranch.

1.    Beheben Sie den Fehler mit Azure Data Factory Studio. Testen Sie die Änderungen.

1.    Wählen Sie nach der Verifizierung der Fehlerbehebung die Option **Export ARM Template** (ARM-Vorlage exportieren) aus, um die Resource Manager-Vorlage für den Hotfix abzurufen.

1.    Checken Sie diesen Build manuell im Branch „adf_publish“ ein.

1.    Wenn Sie die Releasepipeline so konfiguriert haben, dass sie bei Check-Ins in „adf_publish“ automatisch ausgelöst wird, wird automatisch ein neues Release gestartet. Reihen Sie andernfalls manuell ein Release in die Warteschlange ein.

1.    Stellen Sie das Hotfixrelease für die Test- und Produktionsfactorys bereit. Dieses Release enthält die vorherige Nutzlast der Produktionsumgebung sowie die Korrektur, die Sie in Schritt 5 vorgenommen haben.

1.   Fügen Sie dem Entwicklungsbranch die Änderungen aus dem Hotfix hinzu, damit spätere Releases nicht den gleichen Fehler enthalten.

## <a name="video-tutorial"></a>Videotutorial
Das folgende Video ist ein ausführliches Videotutorial, in dem Sie erfahren, wie Sie ein Hotfix in Ihren Umgebungen anwenden. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4I7fi]

## <a name="next-steps"></a>Nächste Schritte

- [Continuous Integration und Continuous Delivery (CI/CD) – Übersicht](continuous-integration-delivery.md)
- [Automatisieren von Continuous Integration mit Azure Pipelines-Releases](continuous-integration-delivery-automate-azure-pipelines.md)
- [Manuelles Höherstufen einer Resource Manager-Vorlage für jede Umgebung](continuous-integration-delivery-manual-promotion.md)
- [Verwenden benutzerdefinierter Parameter mit einer Resource Manager-Vorlage](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [Verknüpfte Resource Manager-Vorlagen](continuous-integration-delivery-linked-templates.md)
- [Beispielskript für vor und nach der Bereitstellung](continuous-integration-delivery-sample-script.md)