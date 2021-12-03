---
title: Erstellen und Anpassen von Microsoft Sentinel-Playbooks aus integrierten Vorlagen | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Playbooks aus Playbookvorlagen erstellen und mit ihnen arbeiten, um sie an Ihre Anforderungen anzupassen.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2034ba667e3d96846aa27e58dce2555da39116b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518829"
---
# <a name="create-and-customize-microsoft-sentinel-playbooks-from-built-in-templates"></a>Erstellen und Anpassen von Microsoft Sentinel-Playbooks aus integrierten Vorlagen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **Playbookvorlagen** befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Eine Playbookvorlage ist ein vordefinierter, getesteter und einsatzbereiter Workflow, der an Ihre Anforderungen angepasst werden kann. Vorlagen können auch als Referenz für bewährte Methoden bei der Entwicklung vollkommen neuer Playbooks oder als Anregung für neue Automatisierungsszenarien dienen.

Playbookvorlagen sind selbst keine aktiven Playbooks, bis Sie daraus ein Playbook (eine bearbeitbare Kopie der Vorlage) erstellen.

Viele Playbookvorlagen wurden von der Microsoft Sentinel-Community, unabhängigen Softwareanbietern (ISVs) und Microsoft-Experten entwickelt und basieren auf beliebten Automatisierungsszenarien, die von Security Operations Centern auf der ganzen Welt verwendet werden.

Sie können Playbookvorlagen aus den folgenden Quellen beziehen:

- Auf der Registerkarte **Playbookvorlagen** (unter **Automatisierung**) werden die führenden Szenarien der Microsoft Sentinel-Community angezeigt. Es können mehrere aktive Playbooks aus derselben Vorlage erstellt werden.

    Wenn eine neue Version der Vorlage veröffentlicht wird, werden die aus dieser Vorlage erstellten aktiven Playbooks (auf der Registerkarte **Playbooks**) mit einer Benachrichtigung gekennzeichnet, dass ein Update verfügbar ist.

- Playbookvorlagen können auch als Teil einer [Microsoft Sentinel-Lösung](sentinel-solutions.md) im Kontext eines bestimmten Produkts abgerufen werden. Durch die Bereitstellung der Lösung werden aktive Playbooks erzeugt.

- Das [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) enthält viele Playbookvorlagen. Diese können in einem Azure-Abonnement bereitgestellt werden, indem auf die Schaltfläche **In Azure bereitstellen** geklickt wird.

Technisch gesehen ist eine Playbookvorlage eine [ARM-Vorlage](../azure-resource-manager/templates/index.yml) (Azure Resource Manager), die aus mehreren Ressourcen besteht: einem Azure Logic Apps-Workflow und API-Verbindungen für jede einbezogene Verbindung.

Dieser Artikel konzentriert sich auf die Bereitstellung einer Playbookvorlage über die Registerkarte **Playbookvorlagen** unter **Automatisierung**.

Dieser Artikel hilft Ihnen bei Folgendem:

> [!div class="checklist"]
> - Erkunden von sofort einsatzbereiten Playbookvorlagen
> - Bereitstellen einer Playbookvorlage

## <a name="explore-playbook-templates"></a>Erkunden von Playbookvorlagen

Wählen Sie im Navigationsmenü von Microsoft Sentinel die Option **Automatisierung** und dann die Registerkarte **Playbookvorlagen** aus.

Die hier gezeigten Playbookvorlagen veranschaulichen führende Automatisierungsszenarien, die SOCs in der Regel verwenden oder von denen sie sich Anregungen holen. Die meisten dieser Playbooks wurden von der Microsoft Sentinel-Community beigetragen und befanden sich ursprünglich im Microsoft Sentinel-GitHub-Repository. Einige dieser Lösungen wurden in Microsoft Sentinel-Lösungen integriert.

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="Screenshot: Playbookkatalog" lightbox="media/use-playbook-templates/gallery.png":::

Um eine Playbookvorlage zu finden, die Ihren Anforderungen entspricht, können Sie die Liste nach den folgenden Kriterien filtern:

- **Trigger** gibt an, dass das Playbook durch die Erstellung von Vorfällen (und daher an eine Automatisierungsregel angefügt werden kann), durch die Erstellung von Warnungen (und daher an eine Analyseregel angefügt werden kann) oder durch etwas anderes ausgelöst wird. [Weitere Informationen](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary)

- **Logic Apps-Connectors** zeigen die externen Dienste an, mit denen dieses Playbook interagieren wird. Während des Bereitstellungsprozesses muss jeder Connector eine Identität annehmen, um sich beim externen Dienst zu authentifizieren.

- **Entitäten** zeigen die Entitätstypen an, die explizit von einem Playbook gefiltert und analysiert werden, das erwartet, dass diese Entitätstypen im Vorfall gefunden werden. Ein Playbook, das eine Firewall anweist, eine IP-Adresse zu sperren, wird z. B. auf Vorfälle reagieren, die von Analyseregeln erstellt wurden, die Warnungen mit IP-Adressen generieren, z. B. eine Regel zur Erkennung von Brute-Force-Angriffen.

- **Tags** zeigen Bezeichnungen an, die auf das Playbook angewendet werden, um es mit einem bestimmten Szenario zu verknüpfen oder um ein besonderes Merkmal anzugeben.

  Beispiele:

  - **Anreicherung**: Das Playbook ruft Informationen von einem anderen Dienst ab, um Informationen zu einem Vorfall hinzuzufügen. Diese Informationen werden in der Regel als Kommentar zum Vorfall hinzugefügt oder an das SOC gesendet.

  - **Wartung**: Das Playbook führt eine Aktion für die betroffenen Entitäten aus, um eine potenzielle Bedrohung zu beseitigen.

  - **Synchronisierung**: Das Playbook hilft dabei, einen externen Dienst, z. B. einen Vorfallverwaltungsdienst, mit den Eigenschaften des Vorfalls zu aktualisieren.

  - **Benachrichtigung**: Das Playbook sendet eine E-Mail oder Nachricht.

  - **Antwort von Teams**: Das Playbook ermöglicht es den Analysten, über Teams mithilfe interaktiver Karten eine manuelle Aktion zu ergreifen.

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="Filtern der Liste der Playbookvorlagen":::

## <a name="customize-a-playbook-from-a-template"></a>Anpassen eines Playbooks aus einer Vorlage

In diesem Verfahren wird beschrieben, wie Playbookvorlagen bereitgestellt werden.

Sie können diesen Vorgang wiederholen, um mehrere Playbooks anhand derselben Vorlage zu erstellen.

1. Wählen Sie auf der Registerkarte **Playbookvorlagen** einen Playbooknamen aus.

1. Wenn das Playbook bestimmte Voraussetzungen aufweist, müssen Sie die Anweisungen befolgen.

    - Einige Playbooks rufen andere Playbooks als Aktionen auf. Dieses zweite Playbook wird als **geschachteltes Playbook** bezeichnet. In einem solchen Fall besteht eine der Voraussetzungen darin, zuerst das geschachtelte Playbook bereitzustellen.

    - Einige Playbooks erfordern die Bereitstellung eines benutzerdefinierten Logic Apps-Connectors oder einer Azure-Funktion. In solchen Fällen wird der Link **In Azure bereitstellen** verwendet. <!-- WHERE? --> Dies führt Sie zum allgemeinen Bereitstellungsprozess für ARM-Vorlagen.

1. Wählen Sie **Playbook erstellen** aus, um den Assistenten zum Erstellen von Playbooks basierend auf der ausgewählten Vorlage zu öffnen. Der Assistent verfügt über vier Registerkarten:

    - **Grundeinstellungen**: Suchen Sie Ihr neues Playbook (Logic Apps-Ressource), und geben Sie ihm einen Namen (Standardwert kann verwendet werden).
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="Assistent zum Erstellen von Playbooks, Registerkarte mit Grundeinstellungen":::

    - **Parameter**: Geben Sie kundenspezifische Werte ein, die vom Playbook verwendet werden. Wenn dieses Playbook z. B. eine E-Mail an das SOC sendet, können Sie hier die Empfängeradresse definieren. Diese Registerkarte wird nur angezeigt, wenn das Playbook über Parameter verfügt.

        > [!NOTE]
        > Wenn dieses Playbook einen benutzerdefinierten Connector verwendet, sollte es in derselben Ressourcengruppe bereitgestellt werden, und Sie können seinen Namen auf dieser Registerkarte einfügen.

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="Assistent zum Erstellen von Playbooks, Registerkarte mit Parametern":::

    - **Verbindungen**: Erweitern Sie jede Aktion, um die vorhandenen Verbindungen anzuzeigen, die Sie für vorherige Playbooks erstellt haben. Erfahren Sie mehr über das [Erstellen von Verbindungen für Playbooks](authenticate-playbooks-to-sentinel.md).

        > [!NOTE]
        > Bei benutzerdefinierten Connectors werden Verbindungen anhand des Namens des benutzerdefinierten Connectors angezeigt, der auf der Registerkarte **Parameter** eingegeben wurde.

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="Assistent zum Erstellen von Playbooks, Registerkarte mit Verbindungen":::

        Wenn keine Verbindungen vorhanden sind oder Sie neue erstellen möchten, wählen Sie **Neue Verbindung nach der Bereitstellung erstellen** aus. Nach Abschluss der Bereitstellung gelangen Sie zum Logic Apps-Designer.

        Für Connectors, die das [Herstellen einer Verbindung mit einer verwalteten Identität](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity) unterstützen, z. B. **Microsoft Sentinel**, ist dies die standardmäßig ausgewählte Verbindungsmethode.

    - **Überprüfen und erstellen**: Zeigen Sie eine Zusammenfassung des Prozesses an, und warten Sie auf die Überprüfung Ihrer Eingabe, bevor Sie das Playbook erstellen.

1. Wenn Sie die Schritte des Assistenten zum Erstellen von Playbooks bis zum Ende durchgeführt haben, gelangen Sie zum Workflowentwurf des neuen Playbooks im Logic Apps-Designer.

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Weitere Informationen finden Sie unter „Playbook in Logic Apps-Designer“":::

1. Für jeden Connector, für den Sie das Erstellen einer neuen Verbindung nach der Bereitstellung ausgewählt haben:
    1. Wählen Sie im Navigationsmenü **API-Verbindungen** aus.
    1. Wählen Sie den Verbindungsnamen aus.
    1. Wählen Sie im Navigationsmenü die Option **API-Verbindung bearbeiten** aus.
    1. Füllen Sie die erforderlichen Parameter aus, und klicken Sie auf **Speichern**.
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="Screenshot: Bearbeiten von API-Verbindungen":::

    Alternativ können Sie innerhalb der relevanten Schritte im Logic Apps-Designer eine neue Verbindung erstellen:
    1. Wählen Sie jeden Schritt aus, der mit einem Fehlerzeichen versehen ist, um ihn zu erweitern.
    1. Wählen Sie **Neue hinzufügen** aus.
    1. Authentifizieren Sie sich gemäß den entsprechenden Anweisungen.
    1. Wenn es andere Schritte gibt, die denselben Connector verwenden, erweitern Sie deren Felder. Wählen Sie in der angezeigten Liste der Verbindungen die Verbindung aus, die Sie gerade erstellt haben.

1. Wenn Sie sich für die Verwendung einer Verbindung mit einer verwalteten Identität für Microsoft Sentinel (oder für andere unterstützte Verbindungen) entschieden haben, gewähren Sie dem neuen Playbook Berechtigungen für den Microsoft Sentinel-Arbeitsbereich (oder für die relevanten Zielressourcen für andere Connectors).

1. Speichern Sie das Playbook. Sie können es jetzt auf der Registerkarte **Aktive Playbooks** sehen.

1. Um dieses Playbook auszuführen, [legen Sie eine automatische Reaktion fest](automate-responses-with-playbooks.md#set-an-automated-response) oder [führen es manuell aus](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert) (nur Warnungstrigger).

1. Die meisten Playbookvorlagen können unverändert verwendet werden. Wir empfehlen jedoch, alle erforderlichen Anpassungen vorzunehmen, um das neue Playbook an Ihre SOC-Anforderungen anzupassen.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="issue-found-a-bug-in-the-playbook"></a>Problem: Fehler im Playbook gefunden

Um einen Fehler zu melden oder eine Verbesserung für ein Playbook anzufordern, wählen Sie den Link **Unterstützt von** im Detailbereich des Playbooks aus. Wenn es sich um ein von der Community unterstütztes Playbook handelt, führt Sie der Link zum Öffnen eines GitHub-Problems. Andernfalls werden Sie zur Seite des Unterstützers weitergeleitet.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie mit Playbookvorlagen arbeiten und Playbooks an Ihre Anforderungen anpassen. Erfahren Sie mehr über Playbooks und die Automatisierung in Microsoft Sentinel:

- [Sicherheitsorchestrierung, Automatisierung und Reaktion (Security Orchestration, Automation and Response, SOAR) in Microsoft Sentinel](automation-in-azure-sentinel.md)
- [Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel](automate-responses-with-playbooks.md)
- [Tutorial: Verwenden von Playbooks mit Automatisierungsregeln in Microsoft Sentinel](tutorial-respond-threats-playbook.md)
- [Authentifizieren von Playbooks bei Microsoft Sentinel](authenticate-playbooks-to-sentinel.md)
- [Verwenden von Triggern und Aktionen in Microsoft Sentinel-Playbooks](playbook-triggers-actions.md)
