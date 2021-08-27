---
title: Installieren der Clienttools
description: Installieren von azdata, kubectl, der Azure CLI, psql, Azure Data Studio (Insider) und der Arc-Erweiterung für Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e328c65eb7453a8e80faa315c0b038e8fa6f13e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355975"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>Installieren von Clienttools zum Bereitstellen und Verwalten von Azure Arc-fähigen Datendiensten

In diesem Artikel werden Ressourcen zum Installieren der Tools zum Verwalten von Arc-fähigen Datendiensten bereitgestellt.

> [!IMPORTANT]
> Wenn Sie ein Update auf ein neues Release ausführen, aktualisieren Sie auf die neueste Version von Azure Data Studio, die Azure Arc-Erweiterung für Azure Data Studio, von Azure (`az`) CLI (Befehlszeilenschnittstelle) und von [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)].
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

Die [`arcdata`-Erweiterung für Azure CLI (`az`)](reference/reference-az-arcdata-dc.md) ersetzt `azdata` für Arc-fähige Datendienste.

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>Tools zum Erstellen und Verwalten von Azure Arc-fähigen Datendiensten

In der folgenden Tabelle werden die allgemeinen Tools aufgelistet, die für die Erstellung und Verwaltung von Azure Arc-fähigen Datendiensten erforderlich sind, und es wird beschrieben, wie diese Tools installiert werden:

| Tool | Erforderlich | BESCHREIBUNG | Installation |
|---|---|---|---|
| Azure CLI (`az`)<sup>1</sup> | Ja | Moderne Befehlszeilenschnittstelle zum Verwalten von Azure-Diensten. Wird verwendet, um Azure-Dienste im Allgemeinen und insbesondere Arc-fähige Datendienste mithilfe der CLI oder in Skripts sowohl für den indirekten Verbindungsmodus (jetzt verfügbar) als auch für den direkten Verbindungsmodus (in Kürze verfügbar) zu verwalten. ([Weitere Informationen](/cli/azure/)). | [Installieren](/cli/azure/install-azure-cli) |
| `arcdata`-Erweiterung für die Azure (`az`) CLI | Ja | Befehlszeilentool zum Verwalten von Arc-fähigen Datendiensten als Erweiterung der Azure CLI (`az`) | [Installieren](install-arcdata-extension.md) |
| Azure Data Studio | Ja | Umfassendes Tool zum Herstellen einer Verbindung mit einer Vielzahl von Datenbanken, einschließlich Azure SQL, SQL Server, PostrgreSQL und MySQL. Erweiterungen für Azure Data Studio bieten eine Verwaltungsfunktion für Azure Arc-fähige Datendienste. | [Installieren](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Arc-Erweiterung für Azure Data Studio | Ja | Erweiterung für Azure Data Studio, die Verwaltungsfunktionen für Azure Arc-fähige Datendienste bereitstellt.| Installation aus dem Erweiterungskatalog in Azure Data Studio.|
| PostgreSQL-Erweiterung in Azure Data Studio | Nein | PostgreSQL-Erweiterung für Azure Data Studio, die Verwaltungsfunktionen für PostgreSQL bereitstellt. | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> Installation aus dem Erweiterungskatalog in Azure Data Studio.|
| Kubernetes CLI (kubectl)<sup>2</sup> | Ja | Befehlszeilentool zum Verwalten des zugrunde liegenden Kubernetes-Clusters ([weitere Informationen](https://kubernetes.io/docs/tasks/tools/install-kubectl/)). | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | Für einige Beispielskripts erforderlich. | Befehlszeilentool zum Übertragen von Daten mit URLs. | [Windows](https://curl.haxx.se/windows/) \| Linux: Installieren von curl-Paket |
| oc | Für Red Hat OpenShift- und Azure Red Hat OpenShift-Bereitstellungen erforderlich |`oc` ist die OpenShift-Befehlszeilenschnittstelle. | [Installing the CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli) (Installieren der Befehlszeilenschnittstelle)



<sup>1</sup> Sie müssen die Azure CLI-Version 2.26.0 oder höher verwenden. Führen Sie bei Bedarf `az --version` aus, um die Version zu ermitteln.

<sup>2</sup> Sie müssen die `kubectl`-Version 1.19 oder höher verwenden. Außerdem sollte die Version von `kubectl` um eins höher oder niedriger als die Nebenversion Ihres Kubernetes-Clusters sein. Wenn Sie eine bestimmte Version auf dem `kubectl`-Client installieren möchten, finden Sie weitere Informationen unter [Installieren von `kubectl` mit curl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl) (Verwenden Sie unter Windows 10 cmd.exe und nicht Windows PowerShell zum Ausführen von curl).

<sup>3</sup> Wenn Sie PowerShell verwenden, ist curl ein Alias für das Cmdlet Invoke-WebRequest.

## <a name="next-steps"></a>Nächste Schritte

[Erstellen des Azure Arc-Datencontrollers](create-data-controller.md)
