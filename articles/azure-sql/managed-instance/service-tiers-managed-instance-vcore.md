---
title: Kaufmodell für virtuelle Kerne
description: Mit dem Kaufmodell für virtuelle Kerne können Sie Compute- und Speicherressourcen einzeln skalieren, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis für die Azure SQL Managed Instance optimieren.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 05/18/2021
ms.openlocfilehash: 16782538918c0477b969f95b7730b38221f97f82
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414959"
---
# <a name="azure-sql-managed-instance---compute-hardware-in-the-vcore-service-tier"></a>Azure SQL Managed Instance – Computehardware auf der Dienstebene des virtuellen Kerns
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel wird das Kaufmodell für virtuelle Kerne für [Azure Managed Instance](sql-managed-instance-paas-overview.md) erläutert. Weitere Informationen zur Wahl zwischen dem V-Kern-Kaufmodell (Kaufmodell für virtuelle Kerne) und dem DTU-Kaufmodell finden Sie unter [Entscheidung zwischen dem V-Kern- und DTU-Kaufmodell](../database/purchasing-models.md).

Das Kaufmodell für virtuelle Kerne (vCore) von Azure SQL Managed Instance hat folgende Eigenschaften:

- Kontrolle über die Hardwaregeneration, um besser auf Compute- und Arbeitsspeicheranforderungen der Workload reagieren zu können
- Preisrabatte für [Azure-Hybridvorteil](../azure-hybrid-benefit.md) und [reservierte Instanzen (RI)](../database/reserved-capacity-overview.md)
- Mehr Transparenz bei den Hardwaredetails für das Computing zur besseren Planung von Migrationsvorgängen von lokalen Bereitstellungen
- [Die Preise für reservierte Instanzen](../database/reserved-capacity-overview.md) sind nur für das V-Kern-Kaufmodell verfügbar. 

## <a name="service-tiers"></a><a id="compute-tiers"></a>Dienstebenen

Als Optionen für die Dienstebene stehen im V-Kern-Kaufmodell „Universell“ und „Unternehmenskritisch“ zur Verfügung. Die Dienstebene definiert ganz allgemein die Speicherarchitektur, die Speicherplatz- und E/A-Grenzwerte sowie Optionen für die Geschäftskontinuität im Zusammenhang mit der Verfügbarkeit und Notfallwiederherstellung.

|**Anwendungsfall**|**Allgemeiner Zweck**|**Unternehmenskritisch**|
|---|---|---|
|Am besten geeignet für:|Die meisten geschäftlichen Workloads. Bietet budgetorientierte, ausgewogene und skalierbare Compute- und Speicheroptionen. |Bietet Geschäftsanwendungen die höchste Resilienz gegenüber Fehlern durch die Verwendung mehrerer isolierter Replikate sowie die höchste E/A-Leistung.|
|Storage|Verwendet Remotespeicher. 32 GB – 8 TB |Verwendet lokalen SSD-Speicher. 32 GB – 4 TB |
|IOPS und Durchsatz (ungefähr)|Weitere Informationen finden Sie in der [Übersicht über die Ressourcenlimits von Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|Weitere Informationen finden Sie in der [Übersicht über die Ressourcenlimits von Azure SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics).|
|Verfügbarkeit|Einzelnes Replikat, keine Replikate mit Leseskalierung|3 Replikate, 1 [Replikat, Leseskalierung](../database/read-scale-out.md),<br/>zonenredundante Hochverfügbarkeit (High Availability, HA)|
|Backups|[Georedundanter Speicher mit Lesezugriff (RA-GRS)](../../storage/common/geo-redundant-design.md), 1–35 Tage (standardmäßig 7 Tage)|[RA-GRS](../../storage/common/geo-redundant-design.md), 1–35 Tage (standardmäßig 7 Tage)|
|In-Memory|Nicht unterstützt|Unterstützt|
||||

### <a name="choosing-a-service-tier"></a>Auswählen einer Dienstebene

Weitere Informationen zum Auswählen einer Dienstebene für die jeweilige Workload finden Sie in den folgenden Artikeln:

- [Wann sollte die Dienstebene „Universell“ gewählt werden?](../database/service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wann sollte die Dienstebene „Unternehmenskritisch“ ausgewählt werden?](../database/service-tier-business-critical.md#when-to-choose-this-service-tier)

## <a name="compute"></a>Compute

SQL Managed Instance Compute bietet eine bestimmte Menge an Computeressourcen, die unabhängig von der Workloadaktivität ständig bereitgestellt werden. Die Abrechnung erfolgt dann zu einem Festpreis pro Stunde für die Menge an bereitgestellten Computeressourcen.

## <a name="hardware-generations"></a>Hardwaregenerationen

Zu den Optionen für die Hardwaregeneration im V-Kern-Modell gehört die Gen5-Serie. Die Hardwaregeneration definiert im Allgemeinen die Compute- und Arbeitsspeicherlimits sowie weitere Eigenschaften, die sich auf die Leistung der Workload auswirken.

### <a name="compute-and-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen

|Hardwaregeneration  |Compute  |Arbeitsspeicher  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz<br>- Bereitstellung von bis zu 24 virtuellen Kernen (1 virtueller Kern = 1 physischer Kern)  |- 7 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 168 GB|
|Gen5     |– Intel&reg; E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, Intel&reg; SP-8160-Prozessoren (Skylake)\* und Intel&reg; 8272CL-Prozessoren (Cascade Lake) mit 2,5 GHz\*<br>- Bereitstellung von bis zu 80 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)|5,1 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 408 GB|

\* In der dynamischen Verwaltungssicht [sys. dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) wird die Hardwaregenerierung für Instanzen mit Intel&reg; SP-8160-Prozessoren (Skylake) als „Gen6“ und die für Instanzen mit Intel&reg; 8272CL-Prozessoren (Cascade Lake) als „Gen7“ angezeigt. Ressourcenlimits für alle Gen5-Instanzen sind unabhängig vom Prozessortyp (Broadwell, Skylake oder Cascade Lake) identisch.

### <a name="selecting-a-hardware-generation"></a>Auswählen einer Hardwaregeneration

Sie können im Azure-Portal die Hardwaregeneration zum Zeitpunkt der Erstellung auswählen, und Sie können die Hardwaregeneration einer SQL Managed Instance ändern.

**So wählen Sie eine Hardwaregeneration beim Erstellen einer Instanz von SQL Managed Instance aus**

Ausführliche Informationen finden Sie unter [Erstellen einer Instanz von SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

Wählen Sie auf der Registerkarte **Grundlagen** im Abschnitt **Compute und Speicher** den Link **Datenbank konfigurieren** aus, und wählen Sie dann die gewünschte Hardwaregeneration aus:

:::image type="content" source="../database/media/service-tiers-vcore/configure-managed-instance.png" alt-text="SQL Managed Instance konfigurieren"  loc-scope="azure-portal":::
  
**So ändern Sie die Hardwaregeneration einer vorhandenen Instanz von SQL Managed Instance**

#### <a name="the-azure-portal"></a>[Azure-Portal](#tab/azure-portal)

Klicken Sie auf der SQL Managed Instance-Seite im Abschnitt „Einstellungen“ auf den Link **Tarif**.

:::image type="content" source="../database/media/service-tiers-vcore/change-managed-instance-hardware.png" alt-text="SQL Managed Instance-Hardware ändern"  loc-scope="azure-portal":::

Auf der Seite „Tarif“ können Sie die Hardwaregenerierung wie in den vorherigen Schritten beschrieben ändern.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie das folgende PowerShell-Skript:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Weitere Informationen finden Sie unter dem Befehl [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance).

#### <a name="the-azure-cli"></a>[Die Azure-CLI](#tab/azure-cli)

Verwenden Sie den folgenden CLI-Befehl:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Weitere Informationen finden Sie unter dem Befehl [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update).

---

### <a name="hardware-availability"></a>Hardwareverfügbarkeit

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Die Gen4-Hardware [wird eingestellt](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) und steht für neue Bereitstellungen nicht mehr zur Verfügung. Alle neuen Instanzen müssen auf Gen5-Hardware bereitgestellt werden.

Gen5 ist in allen öffentlichen Regionen weltweit verfügbar.

## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg lesen Sie [Erstellen einer Instanz von SQL Managed Instance mithilfe des Azure-Portals](instance-create-quickstart.md).
- Preisdetails finden Sie unter folgenden Links. 
    - [Preisseite für Azure SQL Managed Instance Einzelinstanz](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/single/)
    - [Preisseite für Azure SQL Managed Instance-Pools](https://azure.microsoft.com/pricing/details/azure-sql-managed-instance/pools/)
- Ausführliche Informationen zu bestimmten Compute- und Speichergrößen in den Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter [Ressourcenlimits für Pools für Azure SQL Managed Instance, die das vCore-basierte Kaufmodell verwenden](resource-limits.md).
