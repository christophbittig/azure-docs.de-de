---
title: Herstellen von Verbindungen mit BareMetal-Infrastrukturinstanzen in Azure
description: Hier erfahren Sie, wie Sie im Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle BareMetal-Instanzen identifizieren und mit ihnen interagieren.
ms.topic: how-to
ms.date: 07/13/2021
ms.openlocfilehash: b9f5de92ed213d987c7dfac5b3e48f9b565bfff5
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113767137"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Herstellen von Verbindungen mit BareMetal-Infrastrukturinstanzen in Azure

In diesem Artikel werden die Aktionen beschrieben, die Sie im [Azure-Portal](https://portal.azure.com/) für Ihre bereitgestellten BareMetal-Infrastrukturinstanzen ausführen können. 
 
## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters
Ein Azure-Ressourcenanbieter für BareMetal-Instanzen ermöglicht die Sichtbarkeit der Instanzen im Azure-Portal. Der *BareMetalInfrastructure*-Ressourcenanbieter wird standardmäßig über das Azure-Abonnement registriert, das Sie für Bereitstellungen von BareMetal-Instanzen verwenden. Wenn Ihre bereitgestellten BareMetal-Instanzen nicht angezeigt werden, registrieren Sie den Ressourcenanbieter in Ihrem Abonnement. 

Sie können den Ressourcenanbieter für BareMetal-Instanzen im Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle (CLI) registrieren.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Sie müssen Ihre Abonnements im Azure-Portal auflisten und dann auf das Abonnement doppelklicken, das für die Bereitstellung Ihrer BareMetal-Instanzen verwendet wurde.
 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Dienste** aus.

1. Geben Sie im Feld **Alle Dienste****Abonnement** ein, und wählen Sie dann **Abonnements** aus.

1. Wählen Sie in der Abonnentenliste das entsprechende Abonnement aus.

1. Wählen Sie die Option **Ressourcenanbieter** aus, und geben Sie **BareMetalInfrastructure** im Suchfenster ein. Der Ressourcenanbieter sollte wie in der Abbildung als **registriert** angezeigt werden.
 
>[!NOTE]
>Klicken Sie auf **Registrieren**, falls der Ressourcenanbieter nicht registriert ist.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Screenshot der registrierten BareMetal-Instanzen":::

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Melden Sie sich über die Azure-Befehlszeilenschnittstelle bei dem Azure-Abonnement an, das Sie für die Bereitstellung der BareMetal-Instanzen verwenden. Verwenden Sie zum Registrieren des Ressourcenanbieters `BareMetalInfrastructure` den Befehl [az provider register](/cli/azure/provider#az_provider_register):

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

Mit dem Befehl [az provider list](/cli/azure/provider#az_provider_list) zeigen Sie alle verfügbaren Anbieter an.

---

Weitere Informationen zu Ressourcenanbietern finden Sie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>BareMetal-Instanzen im Azure-Portal
 
Geben Sie beim Übermitteln einer Bereitstellungsanforderung für BareMetal-Instanzen das Azure-Abonnement an, das mit den BareMetal-Instanzen verknüpft werden soll. Verwenden Sie dasselbe Abonnement, das Sie zum Bereitstellen der Anwendungsschicht nutzen, die mit den BareMetal-Instanzen arbeitet.
 
Während der Bereitstellung Ihrer BareMetal-Instanzen wird in dem von Ihnen in der Bereitstellungsanforderung verwendeten Azure-Abonnement eine neue [Azure-Ressourcengruppe](../azure-resource-manager/management/manage-resources-portal.md) erstellt. In dieser neuen Ressourcengruppe werden alle BareMetal-Instanzen aufgelistet, die Sie in diesem Abonnement bereitgestellt haben.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Wählen Sie im Azure-Portal für das BareMetal-Abonnement **Ressourcengruppen** aus.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Screenshot der Liste der Ressourcengruppen":::

1. Suchen Sie in der Liste nach der neuen Ressourcengruppe.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Screenshot der BareMetal-Instanz in einer gefilterten Ressourcengruppenliste" lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >Sie können nach dem Abonnement filtern, das Sie zum Bereitstellen der BareMetal-Instanz verwendet haben. Nach dem Filtern nach dem richtigen Abonnement wird Ihnen möglicherweise eine lange Liste von Ressourcengruppen angezeigt. Suchen Sie nach einer mit dem Postfix **-Txxx**, wobei „xxx“ drei Ziffern sind, z. B. **-T250**.

1. Wählen Sie die neue Ressourcengruppe aus, um deren Details anzuzeigen. In der Abbildung wird eine bereitgestellte BareMetal-Instanz angezeigt.
   
   >[!NOTE]
   >Wenn Sie mehrere Mandanten von BareMetal-Instanzen unter demselben Azure-Abonnement bereitgestellt haben, werden mehrere Azure-Ressourcengruppen angezeigt.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um alle Ihre BareMetal-Instanzen anzuzeigen, führen Sie den Befehl [az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) für Ihre Ressourcengruppe aus:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Der Parameter `--output` ist ein globaler Parameter und für alle Befehle verfügbar. Mit dem Wert **table** wird die Ausgabe in einem benutzerfreundlichen Format angezeigt. Weitere Informationen finden Sie unter [Ausgabeformate für Azure CLI-Befehle](/cli/azure/format-output-azure-cli).

---

## <a name="view-the-attributes-of-a-single-instance"></a>Anzeigen von Attributen einer einzelnen Instanz

Sie können die Details einer einzelnen Instanz anzeigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wählen Sie in der Liste der BareMetal-Instanzen die einzelne Instanz aus, die Sie anzeigen möchten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Screenshot der Attribute einer einzelnen BareMetal-Instanz" lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Die dargestellten Attribute unterscheiden sich nicht wesentlich von den Azure-VM-Attributen. Auf der linken Seite werden die Ressourcengruppe, die Azure-Region, der Abonnementname und die ID angezeigt. Wenn Sie Tags zugewiesen haben, werden sie hier ebenfalls angezeigt. Standardmäßig sind den BareMetal-Instanzen keine Tags zugewiesen.
 
Auf der rechten Seite werden der Name der BareMetal-Instanz, das Betriebssystem, die IP-Adresse und die SKU mit der Anzahl der CPU-Threads und dem Arbeitsspeicher angezeigt. Außerdem sehen Sie den Energiezustand und die Hardwareversion (Revision des BareMetal-Instanzstempels). Der Energiezustand gibt an, ob die Hardwareeinheit ein- oder ausgeschaltet ist. Die Details des Betriebssystems geben jedoch keinen Aufschluss darüber, ob es ausgeführt wird.
 
Mögliche Hardwarerevisionen:

* Revision 3 (Rev 3)

* Revision 4 (Rev 4)
 
* Revision 4.2 (Rev 4.2)
 
>[!NOTE]
>Rev 4.2 ist die neueste BareMetal Infrastructure-Revision mit neuem Branding, die die vorhandene Architektur von Rev 4 verwendet. Rev 4 befindet sich näher an den Azure-VM-Hosts (virtuelle Computer). Sie bietet wesentliche Verbesserungen bei der Netzwerklatenz zwischen Azure-VMs und SAP HANA-Instanzen. Sie können über das Azure-Portal auf Ihre BareMetal-Instanzen zugreifen und diese verwalten. Weitere Informationen finden Sie unter [Was ist BareMetal Infrastructure (Vorschau) in Azure?](concepts-baremetal-infrastructure-overview.md).

 
Auf der rechten Seite finden Sie zudem den Namen der [Azure-Näherungsplatzierungsgruppe](../virtual-machines/co-location.md). Die Platzierungsgruppe wird für jede bereitgestellte BareMetal-Instanz automatisch erstellt. Referenzieren Sie die Näherungsplatzierungsgruppe bei der Bereitstellung der Azure-VMs, die die Anwendungsschicht hosten. Verwenden Sie die Näherungsplatzierungsgruppe, die der BareMetal-Instanz zugeordnet ist, um sicherzustellen, dass die Azure-VMs in unmittelbarer Nähe der BareMetal-Instanz bereitgestellt werden.
 
>[!TIP]
>Informationen darüber, wie Sie die Anwendungsschicht im gleichen Azure-Rechenzentrum wie Revision 4.x unterbringen, finden Sie unter [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz](../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Führen Sie zum Anzeigen der Details einer BareMetal-Instanz den Befehl [az baremetalinstance show](/cli/azure/baremetalinstance#az_baremetalinstance_show) aus:

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Wenn Sie sich beim Instanznamen unsicher sind, führen Sie den oben beschriebenen Befehl `az baremetalinstance list` aus.

---
 
## <a name="check-activities-of-a-single-instance"></a>Überprüfen von Aktivitäten einer einzelnen Instanz
 
Sie können die Aktivitäten einer einzelnen BareMetal-Instanz überprüfen. Eine der wichtigsten aufgezeichneten Aktivitäten ist ein Neustart der Instanz. Die aufgelisteten Daten umfassen den Status der Aktivität, den Zeitstempel der ausgelösten Aktivität, die Abonnement-ID und den Azure-Benutzer, der die Aktivität ausgelöst hat.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Screenshot der Aktivitäten einer BareMetal-Instanz" lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Änderungen an den Azure-Metadaten der Instanz werden ebenfalls im Aktivitätsprotokoll aufgezeichnet. Neben dem Neustart sehen Sie die Aktivität **Write BareMetallnstances**. Bei dieser Aktivität werden an der BareMetal-Instanz selbst keine Änderungen vorgenommen, aber es werden Änderungen an den Azure-Metadaten der Einheit dokumentiert.
 
Eine weitere Aktivität, die aufgezeichnet wird, ist das Hinzufügen oder Löschen eines [Tags](../azure-resource-manager/management/tag-resources.md) zu bzw. aus einer Instanz.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Hinzufügen und Löschen eines Azure-Tags zu bzw. aus einer Instanz

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
Sie können Azure-Tags zu einer BareMetal-Instanz hinzufügen oder daraus löschen. Die Zuweisung der Tags erfolgt genauso wie bei der Zuweisung von Tags zu VMs. Wie bei VMs befinden sich die Tags in den Azure-Metadaten. Und für die Tags für BareMetal-Instanzen gelten dieselben Einschränkungen wie für Tags für VMs.
 
Das Löschen von Tags funktioniert auch auf die gleiche Weise wie bei VMs. Sowohl das Anwenden als auch das Löschen eines Tags wird im Aktivitätsprotokoll der BareMetal-Instanz aufgeführt.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Das Zuweisen von Tags zu BareMetal-Instanzen funktioniert auf die gleiche Weise wie bei virtuellen Computern. Wie bei VMs befinden sich die Tags in den Azure-Metadaten. Und für die Tags für BareMetal-Instanzen gelten dieselben Einschränkungen wie für Tags für VMs.

Führen Sie zum Hinzufügen von Tags zu einer BareMetal-Instanz den Befehl [az baremetalinstance update](/cli/azure/baremetalinstance#az_baremetalinstance_update) aus:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Sie verwenden denselben Befehl auch zum Entfernen von Tags:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Überprüfen von Eigenschaften einer Instanz
 
Beim Abrufen der Instanzen können Sie zum Abschnitt „Eigenschaften“ navigieren, um die gesammelten Daten zu den Instanzen anzuzeigen. Erfasste Daten umfassen:
- Azure-Konnektivität
- Speicher-Back-End
- ID der ExpressRoute-Leitung
- Eindeutige Ressourcen-ID
- Abonnement-ID 

Diese Informationen werden in Supportanfragen oder beim Einrichten einer Speichermomentaufnahme-Konfiguration benötigt.
 
Die IP-Adresse des Speicher-NFS ist eine weitere wichtige Information. Sie isoliert Ihren Speicher auf Ihren **Mandanten** im BareMetal-Instanzstapel. Sie verwenden diese IP-Adresse, wenn Sie das [Tool zum Konfigurieren anwendungskonsistenter Momentaufnahmen in Azure](../azure-netapp-files/azacsnap-cmd-ref-configure.md) bearbeiten.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Screenshot der Eigenschafteneinstellungen einer BareMetal-Instanz" lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Neustarten einer BareMetal-Instanz im Azure-Portal

Es gibt verschiedene Situationen, in denen das Betriebssystem einen Neustart nicht abschließen kann, sodass die BareMetal-Instanz neu gestartet werden muss.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Sie können einen Neustart der Instanz direkt im Azure-Portal ausführen:
 
Wählen Sie **Neu starten** und dann **Ja** aus, um den Neustart zu bestätigen.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Screenshot: Ausführen eines Neustarts der BareMetal-Instanz":::
 
Wenn Sie eine BareMetal-Instanz neu starten, kommt es zu einer Verzögerung. Während dieser Verzögerung wechselt der Energiezustand von **Wird gestartet** zu **Gestartet**, was darauf hinweist, dass das Betriebssystem vollständig gestartet wurde. Daher können Sie sich nach einem Neustart erst bei der Einheit anmelden, wenn der Zustand **Gestartet** lautet.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Neustarten einer BareMetal-Instanz den Befehl [az baremetalinstance restart](/cli/azure/baremetalinstance#az_baremetalinstance_restart):

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>Abhängig von der Speichergröße Ihrer BareMetal-Instanz kann der Neustart der Hardware und des Betriebssystems bis zu einer Stunde dauern.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Öffnen einer Supportanfrage für BareMetal-Instanzen
 
Sie können Supportanfragen speziell für BareMetal-Instanzen übermitteln.
1. Erstellen Sie im Azure-Portal unter **Hilfe und Support** eine **[Neue Supportanfrage](https://rc.portal.azure.com/#create/Microsoft.Support)** , und geben Sie die folgenden Informationen für das Ticket an:
 
   - **Issuetyp:** Wählen Sie einen Issuetyp aus.
 
   - **Abonnement:** Wählen Sie Ihr Abonnement aus.
 
   - **Dienst:** BareMetal-Infrastruktur
 
   - **Ressource:** Geben Sie den Namen der Instanz an.
 
   - **Zusammenfassung:** Geben Sie eine Zusammenfassung Ihrer Anfrage ein.
 
   - **Problemtyp:** Wählen Sie einen Problemtyp aus.
 
   - **Problemuntertyp:** Wählen Sie einen Untertyp für das Problem aus.

1. Klicken Sie auf die Registerkarte **Lösungen**, um nach einer Lösung für Ihr Problem zu suchen. Wenn Sie keine Lösung finden können, fahren Sie mit dem nächsten Schritt fort.

1. Wählen Sie die Registerkarte **Details** aus, und wählen Sie aus, ob es sich um ein Problem mit VMs oder BareMetal-Instanzen handelt. Anhand dieser Angaben wird die Supportanfrage an die richtigen Experten weitergeleitet.

1. Geben Sie an, wann das Problem aufgetreten ist, und wählen Sie den Instanzbereich aus.

1. Geben Sie bei Bedarf weitere Details zur Anfrage ein, und laden Sie ggf. eine Datei hoch.

1. Wählen Sie **Überprüfen + erstellen** aus, um die Anforderung zu übermitteln.
 
Es dauert bis zu fünf Werktage, bis ein Supportmitarbeiter Ihre Anfrage bestätigt.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Workloads für BareMetal-Infrastruktur.

> [!div class="nextstepaction"]
> [Was ist SAP HANA in Azure (große Instanzen)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
