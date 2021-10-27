---
title: 'Schnellstart: Bereitstellen und Aktivieren eines verwalteten Azure-HSM'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein verwaltetes HSM mithilfe der Azure-Befehlszeilenschnittstelle bereitstellen und aktivieren.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 06/21/2021
ms.author: mbaldwin
ms.openlocfilehash: 13eba035fc81b6ca26ccf56cefc8c955d9af972e
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132225"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Schnellstart: Bereitstellen und Aktivieren eines verwalteten HSM mithilfe der Azure-Befehlszeilenschnittstelle

Verwaltetes HSM von Azure Key Vault ist ein vollständig verwalteter, hochverfügbarer und standardkonformer Einzelmandanten-Clouddienst, der es Ihnen ermöglicht, kryptografische Schlüssel für Ihre Cloudanwendungen über HSMs zu schützen, die mit **FIPS 140-2 Level 3** validiert sind. Weitere Informationen zu verwalteten HSMs finden Sie in der [Übersicht](overview.md). 

In dieser Schnellstartanleitung wird ein verwaltetes HSM mithilfe der Azure-Befehlszeilenschnittstelle erstellt und aktiviert.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Abonnement für Microsoft Azure. Falls Sie über kein Azure-Abonnement verfügen, können Sie sich für eine [kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial) registrieren.
* Azure-Befehlszeilenschnittstelle ab Version 2.25.0 Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *ContosoResourceGroup* am Standort *centralus*.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location centralus
```

## <a name="create-a-managed-hsm"></a>Erstellen eines verwalteten HSM

Die Erstellung einer Ressource vom Typ „Verwaltetes HSM“ umfasst zwei Schritte:
1. Bereitstellen einer verwalteten HSM-Ressource
1. Aktivieren des verwalteten HSM durch Herunterladen der Sicherheitsdomäne

### <a name="provision-a-managed-hsm"></a>Bereitstellen eines verwalteten HSM

Verwenden Sie den Befehl `az keyvault create`, um ein verwaltetes HSM zu erstellen. Dieses Skript enthält drei erforderliche Parameter: einen Ressourcengruppennamen, einen HSM-Namen und den geografischen Standort.

Für die Erstellung einer Ressource vom Typ „Verwaltetes HSM“ sind folgende Angaben erforderlich:
- Eine Ressourcengruppe, in die es unter Ihrem Abonnement eingefügt wird.
- Azure-Standort.
- Eine Liste mit den anfänglichen Administratoren.

Im Beispiel unten wird ein HSM mit dem Namen **ContosoMHSM** in der Ressourcengruppe **ContosoResourceGroup** am Standort **USA, Mitte** erstellt. Hierbei ist **der derzeit angemeldete Benutzer** der einzige Administrator, und für das vorläufige Löschen gilt ein **Aufbewahrungszeitraum von 28 Tagen**. Weitere Informationen über das vorläufige Löschen für verwaltete HSMs finden Sie [hier](soft-delete-overview.md).

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "centralus" --administrators $oid --retention-days 28
```

> [!NOTE]
> Die Ausführung des Erstellungsbefehls kann einige Minuten dauern. Nach erfolgreichem Abschluss können Sie Ihr HSM aktivieren.

Die Ausgabe dieses Befehls enthält die Eigenschaften des verwalteten HSM, das Sie erstellt haben. Die zwei wichtigsten Eigenschaften sind diese:

* **name:** Im Beispiel lautet der Name „ContosoMHSM“. Sie verwenden diesen Namen für andere Key Vault-Befehle.
* **hsmUri**: In diesem Beispiel lautet der URI https://contosohsm.managedhsm.azure.net. Von Anwendungen, die Ihr HSM über die zugehörige REST-API nutzen, muss dieser URI verwendet werden.

Ihr Azure-Konto verfügt nun über die Berechtigung zum Durchführen von Vorgängen für dieses verwaltete HSM. Derzeit ist noch keine andere Person autorisiert.

### <a name="activate-your-managed-hsm"></a>Aktivieren Ihres verwalteten HSM

Bis zur Aktivierung des HSM sind alle Datenebenenbefehle deaktiviert. Sie können keine Schlüssel erstellen oder Rollen zuweisen. Nur die designierten Administratoren, die im Rahmen des Erstellungsbefehls zugewiesen wurden, können das HSM aktivieren. Zum Aktivieren des HSM muss die [Sicherheitsdomäne](security-domain.md) heruntergeladen werden.

Für die HSM-Aktivierung benötigen Sie Folgendes:
- Mindestens drei RSA-Schlüsselpaare (maximal zehn)
- Geben Sie die Mindestanzahl von Schlüsseln an, die zum Entschlüsseln der Sicherheitsdomäne erforderlich sind (Quorum).

Für die HSM-Aktivierung müssen zwischen drei und zehn öffentliche RSA-Schlüssel an das HSM gesendet werden. Das HSM verschlüsselt die Sicherheitsdomäne mit diesen Schlüsseln und sendet sie zurück. Nach erfolgreichem Herunterladen der Sicherheitsdomäne ist Ihr HSM verwendungsbereit. Darüber hinaus müssen Sie ein Quorum angeben. Hierbei handelt es sich um die Mindestanzahl privater Schlüssel, die zum Entschlüsseln der Sicherheitsdomäne erforderlich sind.

Im folgenden Beispiel wird gezeigt, wie Sie mithilfe von `openssl` drei selbst signierte Zertifikate generieren.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Erstellen und speichern Sie die in diesem Schritt generierten RSA-Schlüsselpaare und die generierte Sicherheitsdomänendatei an einem sicheren Ort.

Verwenden Sie den Befehl `az keyvault security-domain download`, um die Sicherheitsdomäne herunterzuladen und Ihr verwaltetes HSM zu aktivieren. Im folgenden Beispiel werden drei RSA-Schlüsselpaare verwendet. (Für diesen Befehl werden nur öffentliche Schlüssel benötigt.) Das Quorum wird auf „2“ festgelegt.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Speichern Sie die Sicherheitsdomänendatei und die RSA-Schlüsselpaare an einem sicheren Ort. Sie werden für die Notfallwiederherstellung sowie für die Erstellung eines weiteren verwalteten HSM mit der gleichen Sicherheitsdomäne benötigt, um die gemeinsame Nutzung von Schlüsseln zu ermöglichen.

Nach erfolgreichem Herunterladen der Sicherheitsdomäne befindet sich Ihr HSM im aktiven Zustand und ist verwendungsbereit.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [az group delete](/cli/azure/group) aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen. Die Ressourcen können wie folgt gelöscht werden:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- Verschaffen Sie sich einen [Überblick über verwaltetes HSM](overview.md).
- Erfahren Sie mehr über das [Verwalten von Schlüsseln in einem verwalteten HSM](key-management.md).
- Erfahren Sie mehr über die [Rollenverwaltung für ein verwaltetes HSM](role-management.md).
- Sehen Sie sich die [bewährten Methoden für verwaltetes HSM](best-practices.md) an.
