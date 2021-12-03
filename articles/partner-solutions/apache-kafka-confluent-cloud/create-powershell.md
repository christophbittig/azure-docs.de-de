---
title: 'Erstellen von Apache Kafka für Confluent Cloud über Azure PowerShell: Azure-Partnerlösungen'
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure PowerShell eine Instanz von Apache Kafka für Confluent Cloud erstellen.
ms.service: partner-services
ms.topic: quickstart
ms.date: 11/03/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 696e5138d80b1ba799f96fe60da3be0f4f1d2548
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485875"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud---azure-powershell"></a>Schnellstart: Erste Schritte mit Apache Kafka für Confluent Cloud – Azure PowerShell

In dieser Schnellstartanleitung verwenden Sie Azure Marketplace und Azure PowerShell, um eine Instanz von Apache Kafka für Confluent Cloud zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie kein aktives Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
- Sie müssen für Ihr Azure-Abonnement über die Rolle _Besitzer_ oder _Mitwirkender_ verfügen. Die Integration zwischen Azure und Confluent kann nur von Benutzern eingerichtet werden, für die Zugriff als _Besitzer_ oder _Mitwirkender_ gewährt wurde. [Vergewissern Sie sich zunächst, dass Sie über die entsprechende Zugriffsberechtigung verfügen](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Suchen nach dem Angebot

Verwenden Sie das Azure-Portal, um nach der Anwendung „Apache Kafka in Confluent Cloud“ zu suchen.

1. Navigieren Sie in einem Webbrowser zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich an.

1. Falls Sie während einer der letzten Sitzungen den **Marketplace** besucht haben, können Sie das Symbol aus den verfügbaren Optionen auswählen. Suchen Sie andernfalls nach _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace-Symbol":::

1. Auf der Seite **Marketplace** werden je nach gewünschtem Plan zwei Optionen angezeigt. Sie können sich für einen Plan mit nutzungsbasierter Bezahlung oder einen Vertragsplan registrieren. Die nutzungsbasierte Bezahlung ist öffentlich verfügbar. Der Vertragsplan ist für Kunden verfügbar, für die ein privates Angebot genehmigt wurde.

   - Suchen Sie für Kunden mit **nutzungsbasierter Bezahlung** nach _Apache Kafka in Confluent Cloud_. Wählen Sie das Angebot „Apache Kafka in Confluent Cloud“ aus.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Suchen nach dem Azure Marketplace-Angebot":::

   - Wählen Sie für Kunden mit **Vertragsplan** den Link zum **Anzeigen von privaten Angeboten** aus. Beim Vertragsplan müssen Sie sich bei der Registrierung zur Zahlung eines Mindestausgabenbetrags verpflichten. Verwenden Sie diese Option nur, wenn Sie wissen, dass Sie den Dienst längere Zeit benötigen.

     :::image type="content" source="media/view-private-offers.png" alt-text="Anzeigen von privaten Angeboten":::

     Suchen Sie nach _Apache Kafka in Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Auswählen des privaten Angebots":::

## <a name="create-resource"></a>Erstellen von Ressourcen

Nachdem Sie das Angebot für Apache Kafka in Confluent Cloud ausgewählt haben, können Sie mit der Einrichtung der Anwendung beginnen.

Bereiten Sie zunächst Ihre Umgebung für Azure PowerShell vor:

[!INCLUDE [azure-powershell-requirements-no-header.md](../../../includes/azure-powershell-requirements-no-header.md)]

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls **Az.Confluent** verfügbar ist, müssen Sie es separat mithilfe des Cmdlets `Install-Module` installieren.

```azurepowershell
Install-Module -Name Az.Confluent -Scope CurrentUser -Repository PSGallery -Force
```

Verwenden Sie nach der Anmeldung das Cmdlet [New-AzConfluentOrganization](/powershell/module/az.confluent/new-azconfluentorganization), um die neue Organisationsressource zu erstellen:

```azurepowershell
$ConfluentOrgParams = @{
    Name = 'myOrganization'
    ResourceGroupName = 'myResourceGroup'
    Location = 'my location'
    OfferDetailId = 'string'
    OfferDetailPlanId = 'string'
    OfferDetailPlanName = 'string'
    OfferDetailPublisherId = 'string'
    OfferDetailTermUnit = 'string'
    UserDetailEmailAddress = 'contoso@microsoft.com'
    UserDetailFirstName = 'string'
    UserDetailLastName = 'string'
    Tag = @{Environment='Dev'}
}

New-AzConfluentOrganization @ConfluentOrgParams
```

> [!NOTE]
> Wenn der Befehl Informationen zurückgeben soll, bevor der Erstellungsvorgang abgeschlossen ist, fügen Sie den optionalen Parameter `-NoWait` hinzu. Der Vorgang wird ausgeführt, bis die Erstellung der Confluent-Organisation abgeschlossen wurde.

Um eine Liste vorhandener Organisationen anzuzeigen, verwenden Sie das Cmdlet [Get-AzConfluentOrganization](/powershell/module/az.confluent/get-azconfluentorganization).

Sie können alle Organisationen in Ihrem Abonnement anzeigen:

```azurepowershell
Get-AzConfluentOrganization
```

Oder Sie zeigen die Organisationen in einer Ressourcengruppe an:

```azurepowershell
Get-AzConfluentOrganization -ResourceGroupName myResourceGroup
```

Verwenden Sie das Cmdlet `Get-AzConfluentOrganization` mit den Parametern `Name` und `ResourceGroupName`, um die Eigenschaften einer bestimmten Organisation anzuzeigen.

Sie können die Organisationen nach Namen anzeigen:

```azurepowershell
Get-AzConfluentOrganization -Name myOrganization -ResourceGroupName myResourceGroup
```

Falls Sie eine Fehlermeldung erhalten, helfen Ihnen die Informationen unter [Problembehandlung: Apache Kafka in Confluent Cloud-Lösungen](troubleshoot.md) weiter.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten der Confluent Cloud-Ressource](manage.md)
