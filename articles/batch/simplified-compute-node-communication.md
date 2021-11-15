---
title: Verwenden der vereinfachten Kommunikation mit Computeknoten
description: Erfahren Sie, wie der Azure Batch-Dienst die Verwaltung der Batch-Poolinfrastruktur vereinfacht und wie Sie sich dafür an- und abmelden.
ms.topic: how-to
ms.date: 10/21/2021
ms.openlocfilehash: 9bd53bcab9ccb9403d8d851300ba987a23eacc11
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270608"
---
# <a name="use-simplified-compute-node-communication"></a>Verwenden der vereinfachten Kommunikation mit Computeknoten

Ein Azure Batch-Pool enthält einen oder mehrere Serverknoten, die benutzerseitig angegebene Workloads in Form von Batch-Tasks ausführen. Um die Batch-Funktionalität und die Verwaltung der Batch-Poolinfrastruktur zu aktivieren, müssen Serverknoten mit dem Azure Batch-Dienst kommunizieren.

In diesem Dokument werden bevorstehende Änderungen bei der Kommunikation des Azure Batch-Diensts mit Serverknoten im Batch-Pool und die möglicherweise erforderlichen Änderungen an der Netzwerkkonfiguration sowie die Verwendung der neuen vereinfachten Kommunikationsfunktion für Serverknoten für Ihre Batch-Konten während der öffentlichen Vorschauphase beschrieben.

> [!IMPORTANT]
> Die Unterstützung für die vereinfachte Kommunikation von Serverknoten in Azure Batch befindet sich derzeit in der öffentlichen Vorschau. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Das Abonnieren ist derzeit nicht erforderlich. In Zukunft ist jedoch die Verwendung der vereinfachten Kommunikation von Serverknoten für alle Batch-Konten erforderlich. Zu diesem Zeitpunkt wird eine offizielle Benachrichtigung zur Einstellung veröffentlicht, die auch die Möglichkeit bietet, Ihre Batch-Pools vorher zu migrieren.

## <a name="compute-node-communication-changes"></a>Änderungen an der Kommunikation von Serverknoten

Der Azure Batch-Dienst vereinfacht die Verwaltung der Batch-Poolinfrastruktur im Namen von Benutzer*innen. Mit der neuen Kommunikationsmethode reduzieren sich die Komplexität und der Umfang ein- und ausgehender Netzwerkverbindungen, die für Baselinevorgänge erforderlich sind.

Batch-Pools in Konten, die die vereinfachte Kommunikation von Serverknoten nicht abonniert haben, erfordern beim [Erstellen eines Pools in einem virtuellen Netzwerk](batch-virtual-network.md) die folgenden Netzwerkregeln in Netzwerksicherheitsgruppen (NSGs), benutzerdefinierten Routen (User-Defined Routes, UDRs) und Firewalls:

- Eingehend:
  - Zielports 29876 und 29877 über TCP von BatchNodeManagement.*Region*

- Ausgehend:
  - Zielport 443 über TCP an Storage.*Region*
  - Zielport 443 über TCP an BatchNodeManagement.*Region* für bestimmte Workloads, die Kommunikation mit dem Batch-Dienst erfordern, z. B. Auftrags-Manager-Tasks

Mit dem neuen Modell erfordern Batch-Pools in Konten, die die vereinfachte Kommunikation von Serverknoten nutzen, die folgenden Netzwerkregeln in NSGs, UDRs und Firewalls:

- Eingehend:
  - Ohne

- Ausgehend:
  - Zielport 443 über TCP an BatchNodeManagement.*Region*

Ausgehende Anforderungen für ein Batch-Konto können mithilfe der [API zum Auflisten der Endpunkte ausgehender Netzwerkabhängigkeiten](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) ermittelt werden. Diese API gibt den Basissatz von Abhängigkeiten abhängig vom Kommunikationsmodell des Batch-Kontopools zurück. Benutzerspezifische Workloads erfordern möglicherweise zusätzliche Regeln, z. B. das Öffnen von Datenverkehr an andere Azure-Ressourcen (z. B. Azure Storage für Anwendungspakete, Azure Container Registry usw.) oder Endpunkte wie das Microsoft-Paketrepository für die Bereitstellungsfunktionalität für virtuelle Dateisysteme.  

## <a name="benefits-of-the-new-model"></a>Vorteile des neuen Modells

Azure Batch-Benutzer*innen, die [das neue Kommunikationsmodell abonnieren](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication), profitieren von der Vereinfachung von Netzwerkverbindungen und -regeln.

Die vereinfachte Kommunikation von Serverknoten trägt zur Verringerung von Sicherheitsrisiken bei, indem die Anforderung zum Öffnen von Ports für die eingehende Kommunikation aus dem Internet aufgehoben wird. Für den Basisbetrieb ist nur eine einzelne Ausgangsregel für ein bekanntes Diensttag erforderlich.

Das neue Modell bietet auch eine feiner differenzierte Steuerung der Datenexfiltration, da die ausgehende Kommunikation an Storage.*Region* nicht mehr erforderlich ist. Sie können die ausgehende Kommunikation mit Azure Storage explizit sperren, wenn dies für Ihren Workflow erforderlich ist (z. B. für AppPackage-Speicherkonten, andere Speicherkonten für Ressourcendateien oder Ausgabedateien oder andere ähnliche Szenarien).

Selbst wenn Ihre Workloads derzeit nicht von den (im nächsten Abschnitt beschriebenen) Änderungen betroffen sind, sollten Sie trotzdem schon jetzt die [Verwendung der vereinfachten Kommunikation von Serverknoten abonnieren](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication). Damit stellen Sie sicher, dass Ihre Batch-Workloads für zukünftige Verbesserungen bereit sind, die durch dieses Modell möglich werden.

## <a name="scope-of-impact"></a>Umfang der Auswirkungen

In vielen Fällen wirkt sich dieses neue Kommunikationsmodell nicht direkt auf Ihre Batch-Workloads aus. Die vereinfachte Kommunikation von Serverknoten hat jedoch in den folgenden Fällen Auswirkungen:

- Benutzer*innen, die bei der Erstellung eines Batch-Pools ein virtuelles Netzwerk angeben und führen einen oder beide der folgenden Schritte ausführen:
   - Deaktivieren Sie explizit Regeln für ausgehenden Netzwerkdatenverkehr, die mit der vereinfachten Kommunikation von Serverknoten nicht kompatibel sind.
   - Verwenden Sie UDRs und Firewallregeln, die mit der vereinfachten Kommunikation von Serverknoten nicht kompatibel sind.
- Benutzer*innen, die Softwarefirewalls auf Serverknoten aktivieren und ausgehenden Datenverkehr explizit in Softwarefirewallregeln deaktivieren, die mit der vereinfachten Kommunikation von Serverknoten nicht kompatibel sind.

Wenn einer dieser Fälle für Sie gilt und Sie die Vorschauversion abonnieren möchten, führen Sie die im nächsten Abschnitt beschriebenen Schritte aus, um sicherzustellen, dass Ihre Batch-Workloads weiterhin mit dem neuen Modell funktionieren.

### <a name="required-network-configuration-changes"></a>Erforderliche Änderungen an der Netzwerkkonfiguration

Betroffene Benutzer*innen müssen die folgenden Schritte ausführen, um zum neuen Kommunikationsmodell zu migrieren:

1. Stellen Sie sicher, dass Ihre Netzwerkkonfiguration für Batch-Pools (NSGs, UDRs, Firewalls usw.) eine Vereinigung der Modelle enthält (d. h. die Netzwerkregeln vor und nach der vereinfachten Kommunikation von Serverknoten). Diese Regeln sind mindestens erforderlich:
   - Eingehend:
     - Zielports 29876 und 29877 über TCP von BatchNodeManagement.*Region*
   - Ausgehend:
     - Zielport 443 über TCP an Storage.*Region*
     - Zielport 443 über TCP an BatchNodeManagement.*Region*
1. Wenn Für Ihren Workflow zusätzliche Szenarien für ein- oder ausgehenden Datenverkehr erforderlich sind, müssen Sie sicherstellen, dass Ihre Regeln diese Anforderungen erfüllen.
1. [Abonnieren Sie die vereinfachte Kommunikation von Serverknoten](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) wie unten beschrieben.
1. Verwenden Sie eine der folgenden Optionen, um Ihre Workloads für die Verwendung des neuen Kommunikationsmodells zu aktualisieren. Unabhängig davon, welche Methode Sie verwenden, beachten Sie, dass Pools ohne öffentliche IP-Adressen nicht betroffen sind und derzeit die vereinfachte Kommunikation von Serverknoten nicht nutzen können. Beachten Sie den Abschnitt [Aktuelle Einschränkungen](#current-limitations).
   1. Erstellen Sie neue Pools, und überprüfen Sie, ob die neuen Pools ordnungsgemäß funktionieren. Migrieren Sie Ihre Workload zu den neuen Pools, und löschen Sie alle älteren Pools.
   1. Ändern Sie die Größe aller vorhandenen Pools auf 0 Knoten, und skalieren Sie sie anschließend wieder auf.
1. Nachdem Sie sich vergewissert haben, dass alle früheren Pools entweder gelöscht oder auf 0 (null) skaliert und wieder aufskaliert wurden, fragen Sie die [API zum Auflisten der Endpunkte ausgehender Netzwerkabhängigkeiten](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints) ab, um sich zu vergewissern, dass keine Ausgangsregel für Azure Storage für die Region vorhanden ist (mit Ausnahme von Konten für die automatische Speicherung, wenn sie mit Ihrem Batch-Konto verknüpft sind).
1. Ändern Sie alle anwendbaren Netzwerkkonfigurationen mindestens auf die Regeln für die vereinfachte Kommunikation von Serverknoten (beachten eventuelle zusätzlich erforderliche Regeln, wie oben beschrieben):
   - Eingehend:
     - Ohne
   - Ausgehend:
     - Zielport 443 über TCP an BatchNodeManagement.*Region*

Wenn Sie diese Schritte ausführen, aber später die vereinfachte Kommunikation von Serverknoten beenden möchten, gehen Sie wie folgt vor:

1. [Kündigen Sie das Abonnement für die vereinfachte Kommunikation von Serverknoten](#opt-your-batch-account-in-or-out-of-simplified-compute-node-communication) wie unten beschrieben.
1. Migrieren Sie Ihre Workload zu neuen Pools, oder ändern Sie die Größe vorhandener Pools, und skalieren Sie sie wieder auf (siehe Schritt 4 oben).
1. Vergewissern Sie sich mithilfe der [API zum Auflisten der Endpunkte ausgehender Netzwerkabhängigkeiten](/rest/api/batchmanagement/batch-account/list-outbound-network-dependencies-endpoints), dass alle Ihre Pools nicht mehr die vereinfachte Kommunikation von Serverknoten verwenden. Es sollte eine Ausgangsregel für Azure Storage für die Region angezeigt werden (unabhängig von Konten für die automatische Speicherung, die mit Ihrem Batch-Konto verknüpft sind).

## <a name="opt-your-batch-account-in-or-out-of-simplified-compute-node-communication"></a>Einrichten oder Kündigen eines Abonnements für die vereinfachte Kommunikation von Serverknoten für Ihr Batch-Konto

Um für ein Batch-Konto ein Abonnement für die vereinfachte Kommunikation von Serverknoten einzurichten oder zu kündigen, [erstellen Sie eine neue Supportanfrage im Azure-Portal](../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!IMPORTANT]
> Sowohl das Abonnieren als auch das Kündigen der vereinfachten Kommunikation von Serverknoten wirkt sich nur auf das zukünftige Verhalten aus. Batch-Pools mit Serverknoten, die vor der Anforderung erstellt wurden, sind nicht betroffen und nutzen weiter das Modell, das vor der Anforderung aktiv war. Weitere Informationen zum Migrieren vorhandener Pools vor dem Abonnieren oder Kündigen finden Sie in den Migrationsschritten.

Verwenden Sie beim Erstellen Ihrer Anforderung die folgenden Optionen.

:::image type="content" source="media/simplified-compute-node-communication/support-request-opt-in.png" alt-text="Screenshot einer Supportanfrage für das Abonnieren der vereinfachten Kommunikation von Serverknoten":::

1. Wählen Sie als **Problemtyp** die Option **Technisch** aus.
1. Wählen Sie für **Diensttyp** den Eintrag **Batch-Dienst** aus.
1. Wählen Sie unter **Ressource** das Batch-Konto für diese Anforderung aus.
1. Für **Zusammenfassung**:
   - Geben Sie zum Abonnieren „Vereinfachte Kommunikation von Serverknoten aktivieren“ ein.
   - Geben Sie zum Kündigen des Abonnements „Vereinfachte Kommunikation von Serverknoten deaktivieren“ ein.
1. Wählen Sie als **Problemtyp** die Option **Batch-Konten** aus.
1. Wählen Sie als **Problemuntertyp** die Option **Andere Probleme mit Batch-Konten** aus.
1. Wählen **Weiter** und anschließend erneut **Weiter** aus, um zur Seite **Weitere Details** zu navigieren.
1. Unter **Weitere Details** können Sie optional angeben, dass die Aktivierung für alle Batch-Konten in Ihrem Abonnement oder abonnementübergreifend gelten soll. Geben Sie in diesem Fall unbedingt die Abonnement-IDs an.
1. Wählen Sie auf der Seite alle weiteren erforderlichen Informationen und dann **Weiter** aus.
1. Überprüfen Sie die Anforderungsdetails, und wählen Sie dann **Erstellen** aus, um Ihre Supportanfrage zu übermitteln.

Nachdem Ihre Anforderung übermittelt wurde, werden Sie benachrichtigt, sobald das Abonnement für das Konto eingerichtet (oder gekündigt) wurde.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

Im Folgenden finden Sie bekannte Einschränkungen für Konten, die die vereinfachte Kommunikation von Serverknoten abonnieren:

- Das [Erstellen von Pools ohne öffentliche IP-Adressen](batch-pool-no-public-ip-address.md) wird für angemeldete Konten derzeit nicht unterstützt.
- Zuvor erstellte Pools ohne öffentliche IP-Adressen nutzen die vereinfachte Kommunikation von Serverknoten auch dann nicht, wenn das Batch-Konto angemeldet wurde.
- [Private Batch-Konten](private-connectivity.md) können die vereinfachte Kommunikation von Serverknoten abonnieren, aber Batch-Pools, die von diesen Batch-Konten erstellt werden, müssen über öffentliche IP-Adressen verfügen, damit sie die vereinfachte Kommunikation von Serverknoten verwenden können.
- Clouddienstkonfigurations-Pools werden derzeit für die vereinfachte Kommunikation von Serverknoten nicht unterstützt und gelten allgemein als veraltet. Es wird empfohlen, die VM-Konfiguration für Ihre Batch-Pools zu verwenden. Weitere Informationen finden Sie unter [Migrieren der Batch-Poolkonfiguration von Cloud Services zu Virtual Machines](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Pools zu virtuellen Netzwerken](batch-virtual-network.md).
- Erfahren Sie, wie Sie einen [Pool mit angegebenen öffentlichen IP-Adressen erstellen](create-pool-public-ip.md).
