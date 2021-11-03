---
title: Zugriffskonzepte für den Azure Purview Self-Service
description: Verstehen Sie, was Self-Service-Zugriff und Datenermittlung in Azure Purview sind, und finden Sie heraus, wie Benutzer davon profitieren können.
author: bjspeaks
ms.author: blessonj
ms.service: purview
ms.subservice: purview-data-policies
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4b90a7b2bab6801754121d56fc5b0fd7eca8c61
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095414"
---
# <a name="azure-purview-self-service-data-discovery-and-access-preview"></a>Azure Purview Self-Service-Datenermittlung und -Zugriff (Vorschau)

Dieser Artikel hilft Ihnen, die Zugriffsverwaltung des Azure Purview Self-Service zu verstehen.

> [!IMPORTANT]
> Azure Purview Self-Service-Datenermittlung und -Zugriff befinden sich derzeit in VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

## <a name="overview"></a>Übersicht

Azure Purview Self-Service Access Management ermöglicht es Datenkonsumenten, beim Durchsuchen oder Suchen nach Daten Zugriff auf Daten anzufordern. Derzeit unterstützen wir den Self-Service-Datenzugriff auf Speicherkonten, Container, Ordner und Dateien.

Ein **Sammlungsadministrator** muss einer Sammlung einen Self-Service-Workflow zuordnen. Die Sammlung ist eine logische Gruppierung von Datenquellen, die in Azure Purview registriert sind. **Nur Datenquellen, die für die Richtlinienverwaltung registriert sind**, erlauben Datenkonsumenten, Zugriff auf Daten anzufordern.

## <a name="terminology"></a>Begriff

* **Datenverbraucher** ist jeder, der die Daten verwendet. Beispiel: Ein Datenanalyst, der zur Kundensegmentierung auf Marketingdaten zugreift. Datenverbraucher und Datenanforderer werden in diesem Dokument austauschbar verwendet.

* **Sammlung** ist eine logische Gruppierung von Datenquellen, die in Azure Purview registriert sind.

* **Self-Service-Workflow** ist der Prozess, der aufgerufen wird, wenn ein Datenkonsument Zugriff auf Daten anfordert.

* **Genehmiger** sind entweder Sicherheitsgruppen- oder AAD-Benutzer, die Self-Service-Zugriffsanforderungen genehmigen können.

## <a name="how-to-use-azure-purview-self-service-access-management"></a>So verwenden Sie die Self-Service-Zugriffsverwaltung von Azure Purview

Azure Purview ermöglicht es Organisationen, Metadaten zu allen registrierten Datenassets zu katalogisieren. Es ermöglicht Datenkonsumenten, nach dem erforderlichen Datenasset zu suchen oder zu navigieren.  

Mit der Self-Service-Zugriffsverwaltung können Datenkonsumenten nicht nur Datenassets finden, sondern auch Zugriff auf die Datenassets anfordern, die sich in Datenquellen befinden, die für die Richtlinienintegration registriert sind. Wenn der Datenkonsument Zugriff auf ein Datenasset anfordert, wird der zugehörige Self-Service-Zugriffsworkflow ausgelöst.

Eine standardmäßige Self-Service-Workflowvorlage ist verfügbar, wenn ein Azure Purview-Konto erstellt wird.
Die Standardvorlage kann geändert werden, um weitere Genehmiger hinzuzufügen und/oder die E-Mail-Adresse des Genehmigers festzulegen.

Die Rolle des Datenverwalters kann die standardmäßige Self-Service-Workflow-Vorlage anpassen oder den Standard-Workflow ohne Anpassung verwenden. Die Genehmiger für die Zugriffsanfrage werden vom Datenkurator zugewiesen. Dazu kann der Datenkurator entweder eine E-Mail-Verteilergruppe oder die E-Mail-Adresse eines Genehmigers verwenden.

Immer wenn ein Datenkonsument Zugriff auf einen Datensatz anfordert, wird die Benachrichtigung an den Workflow-Genehmiger gesendet. Der Genehmiger kann die Anfrage anzeigen und entweder über das Purview-Portal oder über die E-Mail-Benachrichtigung genehmigen. Wenn die Anfrage genehmigt wird, wird automatisch eine Richtlinie generiert und auf die jeweilige Datenquelle angewendet.

Der Anforderer oder Datenkonsument wird darüber benachrichtigt, dass die Anfrage genehmigt wurde.

Wenn die Datenzugriffsanfrage eines Datenkonsumenten abgelehnt wird, wird keine Richtlinie generiert und der Datenkonsument wird darüber benachrichtigt, dass die Zugriffsanfrage abgelehnt wurde.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie eine Vorschau dieser Funktionen in Ihrer Umgebung anzeigen möchten, folgen Sie dem unten stehenden Link.

-  [Registrieren Sie sich für die Azure Purview-Self-Service-Vorschau](https://aka.ms/opt-in-data-use-policy)
