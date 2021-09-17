---
title: Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc
description: Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: a5aac5704463b28ea9bb124f9fb3120d7a1da52b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102074"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Erstellen einer verwalteten Azure SQL-Instanz in Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>Erstellen einer verwalteten Azure SQL-Instanz

Verwenden Sie den folgenden Befehl, damit verfügbare Optionen für den create-Befehl für die SQL Managed Instance-Instanz angezeigt werden:
```azurecli
az sql mi-arc create --help
```

Führen Sie zum Erstellen einer SQL Managed Instance-Instanz den folgenden Befehl aus:

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

Beispiel:

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  Namen dürfen nicht mehr als 13 Zeichen lang sein und müssen den [DNS-Benennungskonventionen](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names) entsprechen.
>
>  Verwenden Sie beim Angeben einer Arbeitsspeicherbelegung und einer Zuordnung virtueller Kerne diese Formel, um sicherzustellen, dass Ihre Leistung akzeptabel ist. Für jeden virtuellen Kern sollten Sie eine Kapazität von mindestens 4 GB RAM auf dem Kubernetes-Knoten bereitstellen, auf dem der SQL Managed Instance-Pod ausgeführt wird.
>
>  Wenn Sie die Erstellung von SQL-Instanzen automatisieren und die interaktive Eingabeaufforderung für das Administratorenkennwort vermeiden möchten, können Sie die Umgebungsvariablen `AZDATA_USERNAME` und `AZDATA_PASSWORD` auf den gewünschten Benutzernamen und das gewünschte Kennwort festlegen, bevor Sie den Befehl `az sql mi-arc create` ausführen.
> 
>  Wenn Sie den Datencontroller mithilfe von AZDATA_USERNAME und AZDATA_PASSWORD in derselben Terminalsitzung erstellt haben, werden die Werte für AZDATA_USERNAME und AZDATA_PASSWORD ebenfalls verwendet, um die SQL Managed Instance-Instanz zu erstellen.

> [!NOTE]
> Wenn Sie den indirekten Konnektivitätsmodus verwenden, werden die Ressourcen beim Erstellen der Azure SQL Managed Instance-Instanz in Kubernetes nicht automatisch in Azure registriert. Die zum Registrieren der Ressource erforderlichen Schritte finden Sie in den folgenden Artikeln: 
> - [Hochladen von Abrechnungsdaten in Azure und Anzeigen im Azure-Portal](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Anzeigen der Instanz in Azure Arc

Verwenden Sie den folgenden Befehl, um die Instanz anzuzeigen:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Sie können die externe IP-Adresse und Portnummer von dort kopieren und mithilfe eines Tools Ihrer Wahl für die Verbindungsherstellung zu einer SQL Server-/Azure SQL-Instanz wie Azure Data Studio oder SQL Server Management Studio eine Verbindung dazu herstellen.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>Nächste Schritte
- [Herstellen einer Verbindung mit Azure Arc-fähigen SQL Managed Instance-Instanzen](connect-managed-instance.md)
- [Registrieren Ihrer Instanz mit Azure und Hochladen von Metriken und Protokollen für Ihre Instanz](upload-metrics-and-logs-to-azure-monitor.md)
- [Bereitstellen einer Instanz von Azure SQL Managed Instance mithilfe von Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
