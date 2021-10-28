---
title: 'Azure-Schnellstart: Festlegen eines Schlüssels und Abrufen des Schlüssels aus Key Vault mithilfe des Azure-Portals | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals einen Schlüssel in Azure Key Vault festlegen und den Schlüssel daraus abrufen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: f7db6c26dbc80e104bd9fbd85468b54fc4877dec
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217814"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-the-azure-portal"></a>Schnellstart: Festlegen eines Schlüssels und Abrufen des Schlüssels aus Azure Key Vault mit dem Azure-Portal

Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Azure Key Vault-Instanzen können über das Azure-Portal erstellt und verwaltet werden. In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor und verwenden diesen dann, um einen Schlüssel zu speichern. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein Azure-Abonnement, um auf Azure Key Vault zuzugreifen. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Der gesamte Zugriff auf Geheimnisse erfolgt über Azure Key Vault. Für diese Schnellstartanleitung können Sie einen Schlüsseltresor im [Azure-Portal](../general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../general/quick-create-cli.md) oder mit [Azure PowerShell](../general/quick-create-powershell.md) erstellen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="add-a-key-to-key-vault"></a>Hinzufügen eines Schlüssels zu Key Vault

Ein Schlüssel kann dem Tresor mit einigen wenigen Zusatzschritten hinzugefügt werden. In diesem Fall fügen wir einen Schlüssel hinzu, der von einer Anwendung verwendet werden kann. Der Schlüssel heißt **ExampleKey**.

1. Wählen Sie auf den Key Vault-Eigenschaftenseiten die Option **Schlüssel** aus.
2. Klicken Sie auf **Generieren/Importieren**.
3. Wählen Sie auf dem Bildschirm **Schlüssel erstellen** die folgenden Werte aus:
    - **Optionen:** Generieren
    - **Name**: ExampleKey
    - Behalten Sie bei den anderen Optionen die Standardwerte bei. Klicken Sie auf **Erstellen**.

Nachdem Sie die Meldung erhalten haben, dass der Schlüssel erfolgreich erstellt wurde, können Sie in der Liste auf den Schlüssel klicken. Daraufhin werden einige der Eigenschaften angezeigt. 

:::image type="content" source="../media/keys/quick-create-portal/current-version-hidden.png" alt-text="Schlüsseleigenschaften":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials für Key Vault bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.
Wenn Sie die Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Key Vault-Instanz und die dazugehörigen Ressourcen gelöscht. So löschen Sie die Ressourcengruppe über das Portal:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das Suchfeld am oberen Rand des Portals ein. Klicken Sie in den Suchergebnissen auf die Ressourcengruppe aus dieser Schnellstartanleitung.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie im Feld **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen der Ressourcengruppe ein, und klicken Sie anschließend auf **Löschen**.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und einen Schlüssel darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- [Entwicklerhandbuch zu Azure-Schlüsseltresor](../general/developers-guide.md)
- [Azure Key Vault-Sicherheitsübersicht](../general/security-features.md)