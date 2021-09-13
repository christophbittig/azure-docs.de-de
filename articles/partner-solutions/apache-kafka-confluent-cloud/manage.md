---
title: 'Verwalten einer Confluent Cloud-Ressource: Azure-Partnerlösungen'
description: In diesem Artikel wird die Verwaltung einer Confluent Cloud-Ressource im Azure-Portal beschrieben. Hier erfahren Sie, wie Sie das einmalige Anmelden einrichten, eine Confluent-Organisation löschen und Support anfordern.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c1e53382b6f399bccac53a75595eda4e61a915a4
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520994"
---
# <a name="manage-the-confluent-cloud-resource"></a>Verwalten der Confluent Cloud-Ressource

In diesem Artikel wird beschrieben, wie Sie Ihre Instanz von Apache Kafka für Confluent Cloud in Azure verwalten. Sie erfahren, wie Sie das einmalige Anmelden (SSO) einrichten und eine Confluent-Organisation löschen.

## <a name="single-sign-on"></a>Einmaliges Anmelden

Um das einmalige Anmelden für Ihr Unternehmen zu implementieren, kann Ihr Mandantenadministrator die Kataloganwendung importieren. Dieser Schritt ist optional. Informationen zum Importieren einer Anwendung finden Sie unter [Schnellstart: Hinzufügen einer Anwendung zu Ihrem Azure Active Directory (Azure AD)-Mandanten](../../active-directory/manage-apps/add-application-portal.md). Wenn der Mandantenadministrator die Anwendung importiert, müssen Benutzer nicht explizit zustimmen, um den Zugriff für das Confluent Cloud-Portal zuzulassen.

Gehen Sie folgendermaßen vor, um das einmalige Anmelden zu aktivieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zur **Übersicht** für Ihre Instanz der Confluent Cloud-Ressource.
1. Wählen Sie den Link **Manage on Confluent Cloud** (In Confluent Cloud verwalten) aus.

   :::image type="content" source="media/manage/sso-link.png" alt-text="Einmaliges Anmelden im Confluent-Portal":::

1. Wenn der Mandantenadministrator die Kataloganwendung für die Zustimmung zum einmaligen Anmelden nicht importiert hat, gewähren Sie Berechtigungen und erteilen eine Zustimmung. Dieser Schritt wird nur benötigt, wenn Sie zum ersten Mal auf den Link **Manage on Confluent Cloud** zugreifen.

   :::image type="content" source="media/manage/permissions-requested.png" alt-text="Erteilen von Berechtigungen":::

1. Wählen Sie ein Azure AD-Konto für das einmalige Anmelden beim Confluent Cloud-Portal aus.
1. Nachdem die Zustimmung erteilt wurde, werden Sie zum Confluent Cloud-Portal weitergeleitet.

## <a name="set-up-cluster"></a>Einrichten eines Clusters

Weitere Informationen zum Einrichten des Clusters finden Sie in der Confluent-Dokumentation unter [Create a Cluster in Confluent Cloud](https://docs.confluent.io/cloud/current/clusters/create-cluster.html) (Erstellen eines Clusters in Confluent Cloud).

## <a name="delete-confluent-organization"></a>Löschen einer Confluent-Organisation

Wenn Sie Ihre Confluent Cloud-Ressource nicht mehr benötigen, löschen Sie die Ressource in Azure und in Confluent Cloud.

### <a name="portal"></a>[Portal](#tab/azure-portal)

So löschen Sie die Ressourcen in Azure:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Ressourcen** und **Nach Name filtern** für die Confluent Cloud-Ressource oder den **Ressourcentyp** _Confluent organization_ (Confluent-Organisation) aus. Alternativ können Sie im Azure-Portal nach dem Ressourcennamen suchen.
1. Wählen Sie in der **Übersicht** zur Ressource **Löschen** aus.

    :::image type="content" source="media/delete-resources-icon.png" alt-text="Ressource – Löschsymbol":::

1. Geben Sie den Namen der Ressource ein, und wählen Sie **Löschen** aus, um den Löschvorgang zu bestätigen.

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="Aufforderung, die Löschung der Ressource zu bestätigen":::

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Verwenden Sie nach der Anmeldung den Befehl [az confluent organization delete](/cli/azure/confluent#az_confluent_organization_delete), um die Organisationsressource anhand des Namens zu löschen:

```azurecli
az confluent organization delete --name "myOrganization" --resource-group "myResourceGroup"
```

Alternativ ist das Löschen anhand der Ressourcen-ID möglich:

```azurecli
az confluent organization delete --id "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

---

Informationen zum Löschen der Ressource in Confluent Cloud finden Sie in der Confluent-Dokumentation unter [Confluent Cloud Environments](https://docs.confluent.io/current/cloud/using/environments.html) (Confluent Cloud-Umgebungen) und unter [Confluent Cloud Basics](https://docs.confluent.io/current/cloud/using/cloud-basics.html) (Grundlagen zu Confluent Cloud).

Der Cluster und alle Daten im Cluster werden dauerhaft gelöscht. Wenn Ihr Vertrag eine Klausel für die Datenaufbewahrung enthält, werden Ihre Daten von Confluent für den Zeitraum beibehalten, der in der Confluent-Dokumentation unter [Terms of Service](https://www.confluent.io/confluent-cloud-tos) (Vertragsbedingungen) angegeben ist.

Die Nutzung wird Ihnen anteilig bis zum Zeitpunkt der Clusterlöschung in Rechnung gestellt. Nachdem der Cluster dauerhaft gelöscht wurde, erhalten Sie von Confluent eine Bestätigung per E-Mail.

## <a name="next-steps"></a>Nächste Schritte

Hilfe bei der Problembehandlung finden Sie unter [Troubleshooting Apache Kafka for Confluent Cloud solutions](troubleshoot.md) (Problembehandlung in Apache Kafka für Confluent Cloud-Lösungen).

Wenn Sie sich an den Support wenden müssen, finden Sie weitere Informationen unter [Anfordern von Support für Confluent Cloud-Ressourcen](get-support.md).
