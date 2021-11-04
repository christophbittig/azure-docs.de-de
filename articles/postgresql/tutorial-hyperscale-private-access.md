---
title: 'Erstellen einer Servergruppe mit privatem Zugriff (Vorschau) – Hyperscale (Citus): Azure Database for PostgreSQL'
description: Herstellen einer Verbindung zwischen einem virtuellen Computer und dem privaten Endpunkt einer Servergruppe
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: tutorial
ms.date: 10/15/2021
ms.openlocfilehash: 1ae844b4778f8a117bd8ae8807fa28849d9d310d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056346"
---
# <a name="create-server-group-with-private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Erstellen einer Servergruppe mit privatem Zugriff (Vorschau) in Azure Database for PostgreSQL – Hyperscale (Citus)

In diesem Tutorial werden ein virtueller Computer und eine Hyperscale (Citus)-Servergruppe erstellt, und zwischen ihnen wird der [private Zugriff](concepts-hyperscale-private-access.md) eingerichtet.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Zunächst richten Sie eine Ressourcengruppe und ein virtuelles Netzwerk ein. Darin sind später die Servergruppe und der virtuelle Computer enthalten.

```sh
az group create \
    --name link-demo \
    --location eastus

az network vnet create \
    --resource-group link-demo \
    --name link-demo-net \
    --address-prefix 10.0.0.0/16

az network nsg create \
    --resource-group link-demo \
    --name link-demo-nsg

az network vnet subnet create \
    --resource-group link-demo \
    --vnet-name link-demo-net \
    --name link-demo-subnet \
    --address-prefixes 10.0.1.0/24 \
    --network-security-group link-demo-nsg
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Zu Demonstrationszwecken werden ein virtueller Computer mit Debian Linux und der PostgreSQL-Client `psql` verwendet.

```sh
# provision the VM
az vm create \
    --resource-group link-demo \
    --name link-demo-vm \
    --vnet-name link-demo-net \
    --subnet link-demo-subnet \
    --nsg link-demo-nsg \
    --public-ip-address link-demo-net-ip \
    --image debian \
    --admin-username azureuser \
    --generate-ssh-keys

# install psql database client
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts \
        "sudo touch /home/azureuser/.hushlogin" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get update" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-client"
```

## <a name="create-a-server-group-with-a-private-link"></a>Erstellen einer Servergruppe mit einer privaten Verbindung

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

2. Wählen Sie auf der Seite **Neu** die Option **Datenbanken** und dann auf der Seite **Datenbanken** die Option **Azure-Datenbank für PostgreSQL** aus.

3. Wählen Sie als Bereitstellungsoption unter **Hyperscale-Servergruppe (Citus)** die Schaltfläche **Erstellen** aus.

4. Geben Sie im Formular für den neuen Server folgende Informationen an:

    - **Ressourcengruppe**: `link-demo`
    - **Servergruppenname**: `link-demo-sg`
    - **Standort**: `East US`
    - **Kennwort**: (Ihre Wahl)

    > [!NOTE]
    >
    > Der Servergruppenname muss in Azure global eindeutig sein, da damit ein DNS-Eintrag erstellt wird. Wenn `link-demo-sg` nicht verfügbar ist, wählen Sie einen anderen Namen aus, und passen Sie die unten angegebenen Schritte entsprechend an.

5. Wählen Sie **Servergruppe konfigurieren** und dann den Tarif **Basic** aus. Wählen Sie anschließend **Speichern** aus.

6. Klicken Sie auf **Weiter: Netzwerk** aus (im unteren Bereich der Seite).

7. Wählen Sie **Private access (preview)** (Privater Zugriff (Vorschau)) aus.

    > [!NOTE]
    >
    > Privater Zugriff ist nur in [bestimmten Regionen](concepts-hyperscale-limits.md#regions) als Vorschauversion verfügbar.
    >
    > Wenn die Option „Privater Zugriff“ für Ihre Servergruppe nicht ausgewählt werden kann, obwohl sich Ihre Servergruppe in einer zulässigen Region befindet, öffnen Sie eine [Azure-Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und geben Sie Ihre Azure-Abonnement-ID ein, um Zugriff zu erhalten.

8. Ein Bildschirm mit dem Namen **Privaten Endpunkt erstellen** wird angezeigt. Geben Sie die folgenden Werte ein, und wählen Sie **OK** aus:

    - **Ressourcengruppe**: `link-demo`
    - **Standort**: `(US) East US`
    - **Name**: `link-demo-sg-c-pe1`
    - **Untergeordnete Zielressource**: `coordinator`
    - **Virtuelles Netzwerk**: `link-demo-net`
    - **Subnetz**: `link-demo-subnet`
    - **Integration mit einer private DNS-Zone**: Ja

9. Wählen Sie nach dem Erstellen des privaten Endpunkts **Überprüfen und erstellen** aus, um Ihre Hyperscale (Citus)-Servergruppe zu erstellen.

## <a name="access-the-server-group-privately-from-the-virtual-machine"></a>Privates Zugreifen auf die Servergruppe über den virtuellen Computer

Die private Verbindung sorgt dafür, dass der virtuelle Computer eine Verbindung mit der Servergruppe herstellen kann, externe Hosts dagegen nicht. In diesem Schritt überprüfen Sie, ob der Datenbankclient `psql` auf dem virtuellen Computer mit dem Koordinatorknoten der Servergruppe kommunizieren kann.

```sh
# save db URI
#
# obtained from Settings -> Connection Strings in the Azure portal
#
# replace {your_password} in the string with your actual password
PG_URI='host=c.link-demo-sg.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require'

# attempt to connect to server group with psql in the virtual machine
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts "psql '$PG_URI' -c 'SHOW citus.version;'" \
    --query 'value[0].message' \
    | xargs printf
```

In der Ausgabe sollte eine Versionsnummer für Citus angezeigt werden. In diesem Fall konnte psql den Befehl ausführen, und die private Verbindung hat funktioniert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie haben erfahren, wie Sie eine private Verbindung zwischen einem virtuellen Computer und einer Hyperscale (Citus)-Servergruppe erstellen. Nun können Sie die Bereitstellung der Ressourcen aufheben.

Löschen Sie die Ressourcengruppe, und die Bereitstellung der darin enthaltenen Ressourcen wird aufgehoben:

```sh
az group delete --resource-group link-demo

# press y to confirm
```

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über den [privaten Zugriff](concepts-hyperscale-private-access.md) (Vorschau).
* Erhalten Sie weitere Informationen zu [privaten Endpunkten](../private-link/private-endpoint-overview.md).
* Informieren Sie sich ausführlicher über [virtuelle Netzwerke](../virtual-network/concepts-and-best-practices.md).
* Erfahren Sie mehr über [private DNS-Zonen](../dns/private-dns-overview.md).