---
title: Herstellen einer Verbindung mit Azure Arc-fähigen SQL Managed Instance-Instanzen
description: Herstellen einer Verbindung mit Azure Arc-fähigen SQL Managed Instance-Instanzen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: c0a64d5756895f18cbb1285586570ac72dd1c12e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347110"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Herstellen einer Verbindung mit Azure Arc-fähigen SQL Managed Instance-Instanzen

In diesem Artikel wird erläutert, wie Sie eine Verbindung mit Ihrer Azure Arc-fähigen SQL Managed Instance-Instanz herstellen können. 


## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Anzeigen von Azure Arc-fähigen SQL Managed Instance-Instanzen

Verwenden Sie den folgenden Befehl, um die Azure Arc-fähige SQL Managed Instance-Instanz und die externen Endpunkte anzuzeigen:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s -o table
```

Das Ergebnis sollte wie folgt aussehen:

```console
Name       PrimaryEndpoint      Replicas    State
---------  -------------------  ----------  -------
sqldemo    10.240.0.107,1433    1/1         Ready
```

Wenn Sie z. B. AKS oder kubeadm oder OpenShift verwenden, können Sie die externe IP-Adresse und Portnummer hier kopieren und mithilfe eines Tools Ihrer Wahl für die Verbindungsherstellung mit einer SQL Server-/Azure SQL-Instanz wie Azure Data Studio oder SQL Server Management Studio verwenden.  Wenn Sie jedoch die Schnellstart-VM verwenden, finden Sie unten weitere Informationen zum Herstellen einer Verbindung mit der VM außerhalb von Azure. 

> [!NOTE]
> Ihre Unternehmensrichtlinien können den Zugriff auf die IP-Adresse und den Port blockieren, insbesondere, wenn die Erstellung in der öffentlichen Cloud erfolgt.

## <a name="connect"></a>Verbinden 

Herstellen einer Verbindung mit Azure Data Studio, SQL Server Management Studio oder SQLCMD

Öffnen Sie Azure Data Studio, und stellen Sie mit der externen IP-Endpunktadresse und der Portnummer oben eine Verbindung mit Ihrer Instanz her. Wenn Sie eine Azure-VM verwenden, benötigen Sie die _öffentliche_ IP-Adresse, die mit dem [besonderen Hinweis zu Azure-VM-Bereitstellungen](#special-note-about-azure-virtual-machine-deployments) identifiziert werden kann.

Zum Beispiel:

- Server:  52.229.9.30,30913
- Benutzername: sa
- Kennwort: das zum Zeitpunkt der Bereitstellung angegebene SQL-Kennwort

> [!NOTE]
> Sie können Azure Data Studio zum [Anzeigen der SQL Managed Instance-Dashboards](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards) verwenden.

Um eine Verbindung mithilfe von SQLCMD, Linux oder Windows herzustellen, können Sie einen Befehl wie den folgenden verwenden. Geben Sie bei Aufforderung das SQL-Kennwort ein:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Besonderer Hinweis zu Azure-VM-Bereitstellungen

Wenn Sie eine Azure-VM verwenden, zeigt die Endpunkt-IP-Adresse die öffentliche IP-Adresse nicht an. Verwenden Sie den folgenden Befehl, um die externe IP-Adresse zu ermitteln:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sie können die öffentliche IP-Adresse dann mit dem Port kombinieren, um Ihre Verbindung herzustellen.

Möglicherweise müssen Sie den Port der SQL-Instanz über das Netzwerksicherheitsgateway (NSG) zur Verfügung stellen. Sie müssen eine Regel hinzufügen, um Datenverkehr über das Netzwerksicherheitsgateway zuzulassen. Hierzu können Sie den folgenden Befehl verwenden.

Zum Festlegen einer Regel müssen Sie den Namen Ihrer NSG kennen, den Sie mit dem folgenden Befehl ermitteln können:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Sobald Sie den Namen des Netzwerksicherheitsgateways kennen, können Sie mithilfe des folgenden Befehls eine Firewallregel hinzufügen. Mit den hier gezeigten Beispielwerten wird eine Netzwerksicherheitsgateway-Regel für Port 30913 erstellt und das Herstellen von Verbindungen über **beliebige** IP-Quelladressen zugelassen.  Dies ist keine bewährte Methode hinsichtlich der Sicherheit!  Sie können mehr Sicherheit gewährleisten, indem Sie einen Wert „-source-address-prefixes“ festlegen, der spezifisch für Ihre IP-Clientadresse oder einen IP-Adressbereich gilt, der die IP-Adressen Ihres Teams oder Ihrer Organisation umfasst.

Ersetzen Sie den Wert des `--destination-port-ranges`-Parameters unten durch die Portnummer, die Sie aus dem Befehl `az sql mi-arc list` oben abgerufen haben.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen der SQL Managed Instance-Dashboards](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Anzeigen von SQL Managed Instance im Portal](view-arc-data-services-inventory-in-azure-portal.md)
