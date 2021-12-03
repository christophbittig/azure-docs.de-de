---
title: Löschen von Azure-Ressourcen
description: Löschen von Azure-Ressourcen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: ad92c16b70fd2d9f2e137558db1e70c387815a8f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564136"
---
# <a name="delete-resources-from-azure"></a>Löschen von Azure-Ressourcen

In diesem Artikel wird beschrieben, wie Sie Ressourcen von Azure Arc-fähigen Datendiensten aus Azure löschen.

> [!WARNING]
> Wenn Sie Ressourcen wie in diesem Artikel beschrieben löschen, können diese Aktionen nicht rückgängig gemacht werden.

## <a name="before"></a>Vorher

Bevor Sie eine Ressource wie eine verwaltete SQL-Instanz mit Azure Arc-Unterstützung oder einen Azure Arc-Datencontroller löschen, müssen Sie die Nutzungsinformationen exportieren und in Azure hochladen, um eine genaue Abrechnungsberechnung zu erhalten. Befolgen Sie dazu die Anweisungen unter [Hochladen von Abrechnungsdaten in Azure](view-billing-data-in-azure.md#upload-billing-data-to-azure---indirectly-connected-mode).

## <a name="direct-connectivity-mode"></a>Direkter Konnektivitätsmodus

Wenn ein Cluster im direkten Konnektivitätsmodus mit Azure verbunden ist, verwenden Sie das Azure-Portal, um die Ressourcen zu verwalten. Verwenden Sie das Portal für alle CRUD-Vorgänge (Create, Read, Update, & Delete) für die Gruppen Datencontroller, verwaltete Instanz und PostgreSQL.

Über das Azure-Portal:
1. Navigieren Sie zur Ressourcengruppe, und löschen Sie den Azure Arc-Datencontroller.
2. Wählen Sie den Kubernetes-Cluster mit Azure Arc-Unterstützung aus, um zur Übersichtsseite zu navigieren.
    - Wählen Sie unter „Einstellungen“ die Option **Erweiterungen** aus.
    - Wählen Sie auf der Seite „Erweiterungen“ die Erweiterung für Azure Arc-fähige Datendienste (vom Typ „microsoft.arcdataservices“) aus, und klicken Sie auf **Deinstallieren**.
3. Löschen Sie optional den benutzerdefinierten Speicherort, an dem der Azure Arc-Datencontroller bereitgestellt wird.
4. Optional können Sie optional auch den Namespace in Ihrem Kubernetes-Cluster löschen, wenn in dem Namespace keine anderen Ressourcen erstellt werden.



Siehe [Verwalten von Azure-Ressourcen über das Azure-Portal](../../azure-resource-manager/management/manage-resources-portal.md).

## <a name="indirect-connectivity-mode"></a>Indirekter Konnektivitätsmodus

Im indirekten Verbindungsmodus wird eine Instanz durch das Löschen in Kubernetes nicht aus Azure entfernt. Umgekehrt wird eine Instanz nicht durch das Löschen in Azure aus Kubernetes entfernt. Beim indirekten Verbindungsmodus ist das Löschen einer Ressource ein zweistufiger Prozess, der in Zukunft verbessert wird. Kubernetes ist dann die allgemeingültige Datenquelle, und das Portal wird auf der Grundlage dieser Daten aktualisiert.

In einigen Fällen müssen Sie möglicherweise Azure Arc-fähige Datendienstressourcen in Azure manuell löschen.  Sie können die Ressourcen mit einer der folgenden Optionen löschen.

- [Löschen von Azure-Ressourcen](#delete-resources-from-azure)
  - [Löschen einer ganzen Ressourcengruppe](#delete-an-entire-resource-group)
  - [Löschen bestimmter Ressourcen in der Ressourcengruppe](#delete-specific-resources-in-the-resource-group)
  - [Löschen von Ressourcen über die Azure CLI](#delete-resources-using-the-azure-cli)
    - [Löschen von SQL Managed Instance-Ressourcen über die Azure CLI](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Löschen von PostgreSQL Hyperscale-Servergruppenressourcen über die Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Löschen von Azure Arc-Datencontrollerressourcen über die Azure CLI](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Löschen einer Ressourcengruppe über die Azure CLI](#delete-a-resource-group-using-the-azure-cli)


## <a name="delete-an-entire-resource-group"></a>Löschen einer ganzen Ressourcengruppe

Wenn Sie eine bestimmte dedizierte Ressourcengruppe für Azure Arc-fähige Datendienste verwendet haben und Sie *alle* Elemente der Ressourcengruppe löschen möchten, können Sie die Ressourcengruppe samt Inhalt löschen.  

Führen Sie die folgenden Schritte aus, um eine Ressourcengruppe im Azure-Portal zu löschen:

- Navigieren Sie zu der Ressourcengruppe im Azure-Portal, in der die Azure Arc-fähigen Datendienstressourcen erstellt wurden.
- Klicken Sie auf die Schaltfläche **Ressourcengruppe löschen**.
- Bestätigen Sie den Löschvorgang, indem Sie den Ressourcengruppennamen eingeben und auf **Löschen** klicken.

## <a name="delete-specific-resources-in-the-resource-group"></a>Löschen bestimmter Ressourcen in der Ressourcengruppe

Sie können bestimmte Azure Arc-fähige Datendienstressourcen aus einer Ressourcengruppe im Azure-Portal löschen. Führen Sie dazu folgende Schritte aus:

- Navigieren Sie zu der Ressourcengruppe im Azure-Portal, in der die Azure Arc-fähigen Datendienstressourcen erstellt wurden.
- Wählen Sie alle zu löschenden Ressourcen aus.
- Klicken Sie auf die Schaltfläche „Löschen“.
- Bestätigen Sie den Löschvorgang, indem Sie „Ja“ eingeben und auf **Löschen** klicken.

## <a name="delete-resources-using-the-azure-cli"></a>Löschen von Ressourcen über die Azure CLI

Sie können bestimmte Azure Arc-fähige Datendienstressourcen über die Azure CLI löschen.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Löschen von SQL Managed Instance-Ressourcen über die Azure CLI

Um SQL Managed Instance-Ressourcen über die Azure CLI aus Azure zu löschen, ersetzen Sie die Platzhalterwerte im folgenden Befehl und führen den Befehl dann aus.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Löschen von PostgreSQL Hyperscale-Servergruppenressourcen über die Azure CLI

Um PostgreSQL Hyperscale-Servergruppenressourcen über die Azure CLI aus Azure zu löschen, ersetzen Sie die Platzhalterwerte im folgenden Befehl und führen den Befehl dann aus.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Löschen von Azure Arc-Datencontrollerressourcen über die Azure CLI

> [!NOTE]
> Vor dem Löschen eines Azure Arc-Datencontrollers sollten Sie alle Datenbankinstanzressourcen löschen, die von diesem verwaltet werden.

Um Azure Arc-Datencontroller über die Azure CLI aus Azure zu löschen, ersetzen Sie die Platzhalterwerte im folgenden Befehl und führen den Befehl dann aus.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Löschen einer Ressourcengruppe über die Azure CLI

Sie können die Azure CLI auch zum [Löschen einer Ressourcengruppe](../../azure-resource-manager/management/delete-resource-group.md) verwenden.
