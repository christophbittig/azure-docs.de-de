---
title: Erstellen eines Azure-VM-Angebots im Azure Marketplace mit Ihrem eigenen Image
description: Veröffentlichen Sie ein VM-Angebot im Azure Marketplace mit Ihrem eigenen Image.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 11/10/2021
ms.openlocfilehash: 9aa76bf3c26043a93407ee1543225028e795bb9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315367"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>Erstellen einer VM mit einem eigenen Image

In diesem Artikel wird beschrieben, wie Sie ein VM-Image veröffentlichen, das Sie lokal erstellt haben.

## <a name="bring-your-image-into-azure"></a>Einbinden Ihres Image in Azure

Laden Sie Ihre virtuelle Festplatte in eine Azure Compute Gallery (früher bekannt als Shared Image Gallery) hoch.

1. Suchen Sie im Azure-Portal nach **Azure Compute Galleries**.
2. Erstellen oder verwenden Sie eine vorhandene Azure Compute Gallery. Wir empfehlen, eine separate Azure Compute Gallery für Profile zu erstellen, die im Marketplace veröffentlicht werden.
3. Erstellen Sie eine Imagedefinition oder verwenden Sie eine bestehende.
4. Wählen Sie **Version erstellen** aus.
5. Wählen Sie die Region und die Imageversion aus.
6. Wenn Ihre VHD noch nicht ins Azure-Portal hochgeladen wurde, wählen Sie **Speicher-Blobs (VHDs)** als **Quelle** und dann **Durchsuchen aus**. Falls nicht bereits geschehen, können Sie ein **Speicherkonto** und einen **Speichercontainer** erstellen. Laden Sie Ihre VHD hoch.
7. Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Überprüfung **Erstellen** aus.

> [!TIP]
> Das Publisher-Konto muss über den Zugriff "Eigentümer" verfügen, um das Azure Compute Gallery Image zu veröffentlichen. Führen Sie bei Bedarf die Schritte im folgenden Abschnitt – **Festlegen der richtigen Berechtigungen** – aus, um Zugriff zu gewähren.

## <a name="set-the-right-permissions"></a>Festlegen der richtigen Berechtigungen

Wenn Ihr Partner Center-Konto der Inhaber des Abonnements ist, das die Azure Compute Gallery hostet, ist für Berechtigungen nichts weiter erforderlich.

Wenn Sie nur Lesezugriff auf das Abonnement haben, verwenden Sie eine der beiden folgenden Optionen.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>Option 1: Besitzer bitten, die Besitzerberechtigung zu erteilen

Schritte zum Erteilen der Besitzerberechtigung durch den Besitzer:

1. Gehen Sie zur Azure Compute Gallery.
2. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
3. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.<br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="Screenshot des Fensters „Rollenzuweisung hinzufügen“":::
1. Wählen Sie für **Rolle** die Option **Besitzer** aus.
1. Wählen Sie für **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
1. Geben Sie die unter **Auswählen** die Azure-E-Mail-Adresse der Person ein, die das Image veröffentlichen wird.
1. Wählen Sie **Speichern** aus.

### <a name="option-two--run-a-command"></a>Option 2: Befehl ausführen

Bitten Sie den Besitzer, einen dieser Befehle auszuführen (verwenden Sie in beiden Fällen die Abonnement-ID des Abonnements, in dem Sie die Azure Compute Gallery erstellt haben).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> Sie müssen keine SAS-URIs generieren, da Sie jetzt ein Azure Compute Gallery Image im Partner Center veröffentlichen können, ohne APIs zu verwenden. <br/> <br/>Wenn Sie *mit APIs veröffentlichen*, müssen Sie SAS-URIs generieren, anstatt eine Azure Compute Gallery zu verwenden, siehe [Wie generiere ich einen SAS-URI für ein VM-Image](azure-vm-get-sas-uri.md).

## <a name="next-steps"></a>Nächste Schritte

- [Testen Sie das VM-Image](azure-vm-image-test.md), um sicherzustellen, dass es die Veröffentlichungsanforderungen von Azure Marketplace erfüllt (optional).
- Wenn Sie Ihr virtuelles Abbild nicht testen möchten, melden Sie sich beim [Partner Center](https://go.microsoft.com/fwlink/?linkid=2165935) an und veröffentlichen Sie das Azure Compute Gallery Image.
- Wenn beim Erstellen der neuen Azure-basierten VHD Probleme auftreten, informieren Sie sich in den [FAQ zu VMs für Azure Marketplace](azure-vm-create-faq.yml).
