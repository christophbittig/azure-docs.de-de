---
title: Konfigurieren einer IP-Firewall
titleSuffix: Azure Cognitive Search
description: Konfigurieren Sie IP-Steuerungsrichtlinien, um den Zugriff auf Ihren Azure Cognitive Search-Dienst auf bestimmte IP-Adressen einzuschränken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/19/2021
ms.openlocfilehash: e403a71525a8400f47dee01c14ac192c13ab3826
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223182"
---
# <a name="configure-an-ip-firewall-for-azure-cognitive-search"></a>Konfigurieren einer IP-Firewall für Azure Cognitive Search

Azure Cognitive Search unterstützt IP-Regeln für den eingehenden Zugriff über eine Firewall, ähnlich wie die IP-Regeln, die Sie in einer Azure-Sicherheitsgruppe für virtuelle Netzwerke finden. Durch die Nutzung von IP-Regeln können Sie den Suchdienstzugriff auf genehmigte Computer und Clouddienste einschränken. Für den Zugriff auf gespeicherte Daten in Ihrem Suchdienst über diese genehmigten Gruppen von Computern und Diensten muss der Aufrufer weiterhin ein gültiges Autorisierungstoken vorlegen.

Sie können IP-Regeln im Azure-Portal festlegen, wie in diesem Artikel beschrieben, für Suchdienste, die auf der Basic-Ebene und höher bereitgestellt werden. Alternativ können Sie die [Verwaltungs-REST-API Version 2020-03-13](/rest/api/searchmanagement/), [Azure PowerShell](/powershell/module/az.search) oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/search) verwenden.

<a id="configure-ip-policy"></a> 

## <a name="set-ip-ranges-in-azure-portal"></a>Einrichten von IP-Adressbereichen im Azure-Portal

Um die IP-Zugriffssteuerungsrichtlinie im Azure-Portal festzulegen, wechseln Sie zur Seite des Azure Cognitive Search-Diensts, und wählen Sie im linken Navigationsbereich **Netzwerk** aus. Die Endpunktnetzwerkkonnektivität muss **Öffentlicher Zugriff** sein. Wenn Ihre Konnektivität auf **Privater Zugriff** oder **Freigegebener privater Zugriff** festgelegt ist, können Sie nur über einen privaten Endpunkt auf Ihren Suchdienst zugreifen.

:::image type="content" source="media/service-configure-firewall/azure-portal-firewall.png" alt-text="Screenshot: Konfigurieren der IP-Firewall im Azure-Portal" border="true":::

Das Azure-Portal bietet die Möglichkeit, IP-Adressen und IP-Adressbereiche im CIDR-Format anzugeben. Ein Beispiel für die CIDR-Notation ist „8.8.8.0/24“, womit die IPs im Bereich zwischen 8.8.8.0 und 8.8.8.255 dargestellt werden.

Nach dem Aktivieren der IP-Zugriffssteuerungsrichtlinie für Ihren Azure Cognitive Search-Dienst werden alle Anforderungen an die Datenebene von Computern außerhalb der Liste der zulässigen IP-Adressbereiche abgelehnt. 

## <a name="allow-access-from-azure-portal"></a>Zulassen des Zugriffs vom Azure-Portal

Werden IP-Regeln konfiguriert, werden standardmäßig einige Features des Azure-Portals deaktiviert. Sie können Informationen auf Dienstebene anzeigen und verwalten, aber der Portalzugriff auf Indizes, Indexer und andere Ressourcen der obersten Ebene ist eingeschränkt.

Um die Dienstverwaltung über das Portal beizubehalten, wählen Sie unter **Ausnahmen** die Option "Zugriff zulassen" aus. Verwenden Sie alternativ die [VS Code-Erweiterung](https://aka.ms/vscode-search), um Inhalte zu verwalten.

## <a name="allow-access-from-your-client"></a>Zulassen des Zugriffs von Ihrem Client

Clientanwendungen, die Indizierungs- und Abfrageanforderungen per Push an den Suchdienst senden, müssen in einem IP-Adressbereich repräsentiert sein. In Azure können Sie im Allgemeinen die IP-Adresse ermitteln, indem Sie den FQDN eines Diensts pingen (z. B. gibt `ping <your-search-service-name>.search.windows.net` die IP-Adresse eines Suchdiensts zurück). 

Die Bereitstellung von IP-Adressen für Clients stellt sicher, dass die Anforderung nicht direkt abgelehnt wird, aber für den erfolgreichen Zugriff auf Inhalte und Vorgänge ist auch eine Autorisierung erforderlich. Verwenden Sie eine der folgenden Methoden, um Ihre Anforderung zu authentifizieren:

+ [Schlüsselbasierte Authentifizierung](search-security-api-keys.md), bei der ein Administrator- oder Abfrage-API-Schlüssel für die Anforderung bereitgestellt wird
+ [Rollenbasierte Autorisierung](search-security-rbac.md), bei der die aufrufende Funktion Mitglied einer Sicherheitsrolle für einen Suchdienst ist und die [registrierte App ein OAuth-Token](search-howto-aad.md) aus Azure Active Directory darstellt.

### <a name="rejected-requests"></a>Abgelehnte Anforderungen

Wenn Anforderungen von IP-Adressen stammen, die nicht in der Liste zulässig sind, wird eine generische **403 Forbidden-Antwort** ohne zusätzliche Details zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre Clientanwendung eine statische Web-App in Azure ist, erfahren Sie, wie Sie ihren IP-Adressbereich für die Aufnahme in eine IP-Firewallregel des Suchdiensts bestimmen.

> [!div class="nextstepaction"]
> [Ein- und ausgehende IP-Adressen in Azure App Service](../app-service/overview-inbound-outbound-ips.md)