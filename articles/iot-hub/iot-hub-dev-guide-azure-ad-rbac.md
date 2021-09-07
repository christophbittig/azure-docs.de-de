---
title: Steuern des Zugriffs auf IoT Hub mithilfe von Azure Active Directory | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Steuern des Zugriffs auf IoT Hub für Back-End-Apps mithilfe von Azure AD und Azure RBAC.'
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: b6ea56942580b3b8785dcf2b694b30ad64e8a258
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894957"
---
# <a name="control-access-to-iot-hub-using-azure-active-directory"></a>Steuern des Zugriffs auf IoT Hub mithilfe von Azure Active Directory

Azure IoT Hub unterstützt die Verwendung von Azure Active Directory (AAD), um Anforderungen an seine Dienst-APIs wie das Erstellen einer Geräteidentität oder das Aufrufen einer direkten Methode zu authentifizieren. Darüber hinaus unterstützt IoT Hub die Autorisierung der gleichen Dienst-APIs auch mit der rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC). Zusammen können Sie einem AAD-Sicherheitsprinzipal, bei dem es sich um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handeln kann, Berechtigungen für den Zugriff auf die Dienst-APIs von IoT Hub gewähren.

Die Authentifizierung des Zugriffs mit Azure AD und das Steuern von Berechtigungen mit Azure RBAC bieten gemeinsam eine höhere Sicherheit und Benutzerfreundlichkeit als [Sicherheitstoken](iot-hub-dev-guide-sas.md). Um die bei Sicherheitstoken inhärenten potenziellen Sicherheitsrisiken zu minimieren, empfiehlt Microsoft, nach Möglichkeit Azure AD mit Ihrem IoT-Hub zu verwenden.

> [!NOTE]
> Die Authentifizierung mit Azure AD wird für die *Geräte-APIs* von IoT Hub nicht unterstützt (z. B. Gerät-zu-Cloud-Nachrichten und Aktualisieren gemeldeter Eigenschaften). Verwenden Sie [symmetrische Schlüssel](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) oder [X.509](iot-hub-x509ca-overview.md), um Geräte bei IoT Hub zu authentifizieren.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Wenn ein Azure AD-Sicherheitsprinzipal Zugriff auf eine IoT Hub-Dienst-API anfordert, wird zunächst die Identität des Prinzipals *authentifiziert*. Für diesen Schritt muss jede Anforderung zur Laufzeit ein OAuth 2.0-Zugriffstoken enthalten. Der Ressourcenname zum Anfordern des Tokens lautet `https://iothubs.azure.net`. Wenn die Anwendung in einer Azure-Ressource wie z. B. einer Azure-VM, einer Azure-Funktions-App oder einer App Service-App ausgeführt wird, kann sie als [verwaltete Identität](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md) dargestellt werden. 

Nachdem der Azure AD-Prinzipal authentifiziert wurde, ist der zweite Schritt die *Autorisierung*. In diesem Schritt überprüft IoT Hub mit dem Rollenzuweisungsdienst von Azure AD, über welche Berechtigungen der Prinzipal verfügt. Wenn die Berechtigungen des Prinzipals für die angeforderte Ressource oder API ausreichen, autorisiert IoT Hub die Anforderung. Dieser Schritt erfordert also, dass dem Sicherheitsprinzipal mindestens eine Azure-Rolle zugewiesen wird. IoT Hub bietet einige integrierte Rollen, die allgemeine Berechtigungsgruppen umfassen.

## <a name="manage-access-to-iot-hub-using-azure-rbac-role-assignment"></a>Verwalten des Zugriffs auf IoT Hub mithilfe der Azure RBAC-Rollenzuweisung

Bei Azure AD und Azure RBAC erfordert IoT Hub, dass der Prinzipal, der die API anfordert, über eine ausreichende Berechtigungsebene für die Autorisierung verfügt. Um dem Prinzipal diese Berechtigung zu erteilen, gewähren Sie ihm eine Rollenzuweisung. 

- Wenn es sich bei dem Prinzipal um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handelt, befolgen Sie die Anweisungen unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
- Wenn der Prinzipal eine verwaltete Identität ist, befolgen Sie die Anweisungen unter [Zuweisen des Zugriffs einer verwalteten Identität auf eine Ressource über das Azure-Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

Um sicherzustellen, dass Sie nur die geringsten Rechte gewähren, weisen Sie immer eine geeignete Rolle im geringstmöglichen [Ressourcenbereich](#resource-scope) zu, der wahrscheinlich der IoT Hub-Bereich ist.

IoT Hub stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf die IoT Hub-Dienst-API mit Azure AD und Azure RBAC bereit:

| Rolle | BESCHREIBUNG | 
| ---- | ----------- | 
| [Mitwirkender an IoT Hub-Daten](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | Ermöglicht Vollzugriff für Vorgänge auf der IoT Hub-Datenebene |
| [IoT Hub-Datenleser](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | Ermöglicht vollständigen Lesezugriff auf Eigenschaften auf der IoT Hub-Datenebene |
| [Mitwirkender an IoT Hub-Registrierung](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | Ermöglicht Vollzugriff auf die IoT Hub-Geräteregistrierung |
| [Mitwirkender an IoT Hub-Zwillingen](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | Ermöglicht Lese- und Schreibzugriff auf alle IoT Hub-Geräte- und -Modulzwillinge. |

Sie können auch benutzerdefinierte Rollen für IoT Hub festlegen, indem Sie die benötigten [Berechtigungen](#permissions-for-iot-hub-service-apis) kombinieren. Weitere Informationen finden Sie unter [Erstellen benutzerdefinierter Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/custom-roles.md).

### <a name="resource-scope"></a>Ressourcenumfang

Bevor Sie einem Sicherheitsprinzipal eine Azure RBAC-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren. Azure RBAC-Rollen, die in einem umfassenderen Bereich definiert sind, werden von den darunterliegenden Ressourcen geerbt.

In der folgenden Liste werden die Ebenen beschrieben, auf denen Sie den Zugriff auf IoT Hub einschränken können, beginnend mit dem kleinstmöglichen Bereich:

- **Der IoT-Hub:** In diesem Bereich gilt eine Rollenzuweisung nur für den IoT-Hub. Es gibt keinen kleineren Bereich als einen einzelnen IoT-Hub. Rollenzuweisungen in kleineren Bereichen, z. B. für einzelne Geräteidentitäten oder Zwillingsabschnitte, werden nicht unterstützt.
- **Die Ressourcengruppe**. In diesem Bereich gilt eine Rollenzuweisung für alle IoT-Hubs in der Ressourcengruppe.
- **Das Abonnement**. In diesem Bereich gilt eine Rollenzuweisung für alle IoT-Hubs in allen Ressourcengruppen im Abonnement.
- **Eine Verwaltungsgruppe**. In diesem Bereich gilt eine Rollenzuweisung für alle IoT-Hubs in allen Ressourcengruppen in sämtlichen Abonnements der Verwaltungsgruppe.

## <a name="permissions-for-iot-hub-service-apis"></a>Berechtigungen für IoT Hub-Dienst-APIs

In den folgenden Tabellen werden die Berechtigungen beschrieben, die für Vorgänge mit der IoT Hub-Dienst-API verfügbar sind. Damit ein Client einen bestimmten Vorgang aufrufen kann, müssen Sie sicherstellen, dass die zugewiesene RBAC-Rolle des Clients über ausreichende Berechtigungen für diesen Vorgang verfügt.

| RBAC-Aktion | BESCHREIBUNG |
|-|-|
| Microsoft.Devices/IotHubs/devices/read | Liest die Identität eines beliebigen Geräts oder Moduls. |
| Microsoft.Devices/IotHubs/devices/write  | Erstellt oder aktualisiert die Identität des Geräts oder Moduls.  |
| Microsoft.Devices/IotHubs/devices/delete | Löscht die Identität des Geräts oder Moduls. |
| Microsoft.Devices/IotHubs/twins/read | Liest die Identität eines beliebigen Geräte- oder Modulzwillings. |
| Microsoft.Devices/IotHubs/twins/write | Schreibt die Identität eines beliebigen Geräte- oder Modulzwillings. |
| Microsoft.Devices/IotHubs/jobs/read | Gibt eine Liste von Aufträgen zurück. |
| Microsoft.Devices/IotHubs/jobs/write | Erstellt oder aktualisiert einen beliebigen Auftrag. |
| Microsoft.Devices/IotHubs/jobs/delete | Löscht einen beliebigen Auftrag. |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action | Sendet Cloud-zu-Gerät-Nachrichten an ein beliebiges Gerät.  |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action | Empfangen, Abschließen oder Verwerfen von Feedbackbenachrichtigungen von Cloud-zu-Gerät-Nachrichten. |
| Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action | Löscht alle ausstehenden Befehle für ein Gerät.  |
| Microsoft.Devices/IotHubs/directMethods/invoke/action | Ruft eine direkte Methode auf einem beliebigen Gerät oder Modul auf. |
| Microsoft.Devices/IotHubs/fileUpload/notifications/action  | Empfangen, Abschließen oder Abbrechen von Dateiuploadbenachrichtigungen. |
| Microsoft.Devices/IotHubs/statistics/read | Liest Geräte- und Dienststatistiken. |
| Microsoft.Devices/IotHubs/configurations/read | Liest Geräteverwaltungskonfigurationen. |
| Microsoft.Devices/IotHubs/configurations/write | Erstellt oder aktualisiert Geräteverwaltungskonfigurationen. |
| Microsoft.Devices/IotHubs/configurations/delete | Löscht Geräteverwaltungskonfigurationen. |
| Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action  | Wendet den Konfigurationsinhalt auf ein Edgegerät an. |
| Microsoft.Devices/IotHubs/configurations/testQueries/action | Überprüft die Zielbedingung und benutzerdefinierte Metrikabfragen für eine Konfiguration. |

> [!TIP]
> - Für den Vorgang [Massenregistrierungsaktualisierung](/rest/api/iothub/service/bulkregistry/updateregistry) sind *sowohl* `Microsoft.Devices/IotHubs/devices/write` *als auch* `Microsoft.Devices/IotHubs/devices/delete` erforderlich.
> - Für den Vorgang [Zwillingsabfrage](/rest/api/iothub/service/query/gettwins) ist `Microsoft.Devices/IotHubs/twins/read` erforderlich.
> - Das [Abrufen des digitalen Zwillings](/rest/api/iothub/service/digitaltwin/getdigitaltwin) erfordert `Microsoft.Devices/IotHubs/twins/read`, während das [Aktualisieren des digitalen Zwillings](/rest/api/iothub/service/digitaltwin/updatedigitaltwin) `Microsoft.Devices/IotHubs/twins/write` erfordert.
> - Sowohl das [Aufrufen eines Komponentenbefehls](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) als auch das [Aufrufen eines Befehls auf Stammebene](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand) erfordern `Microsoft.Devices/IotHubs/directMethods/invoke/action`.

> [!NOTE]
> Zum Abrufen von Daten aus IoT Hub mithilfe von Azure AD müssen Sie das [Routing zu einem separaten Event Hub einrichten](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint). Um auf den [mit Event Hub kompatiblen integrierten Endpunkt](iot-hub-devguide-messages-read-builtin.md) zuzugreifen, verwenden Sie wie zuvor die Methode mit einer Verbindungszeichenfolge (freigegebener Zugriffsschlüssel). 

## <a name="azure-ad-access-from-azure-portal"></a>Azure AD-Zugriff über das Azure-Portal

Für den Zugriff auf IoT Hub wird im Azure-Portal zunächst überprüft, ob Ihnen eine Azure-Rolle mit **Microsoft.Storage/iotHubs/listkeys/action** zugewiesen ist. In diesem Fall verwendet das Azure-Portal die Schlüssel aus SAS-Richtlinien für den Zugriff auf IoT Hub. Andernfalls versucht das Azure-Konto über Ihr Azure AD-Konto auf die Daten zuzugreifen. 

Um mit Ihrem Azure AD-Konto über das Azure-Portal auf IoT Hub zugreifen zu können, benötigen Sie Berechtigungen für den Zugriff auf die IoT Hub-Datenressourcen (z. B. Geräte und Zwillinge) und Berechtigungen für das Navigieren zur IoT Hub-Ressource im Azure-Portal. Die von IoT Hub bereitgestellten integrierten Rollen gewähren Zugriff auf Ressourcen wie Geräte und Zwillinge, aber nicht auf die IoT Hub-Ressource. Daher erfordert der Zugriff auf das Portal auch die Zuweisung einer ARM-Rolle (Azure Resource Manager) wie [Leser](../role-based-access-control/built-in-roles.md#reader). Die Rolle Leser eignet sich gut, da sie die eingeschränkteste Rolle ist, mit der Sie im Portal navigieren können, und nicht die Berechtigung **Microsoft.Devices/iotHubs/listkeys/action** umfasst (die den Zugriff auf alle IoT Hub-Datenressourcen über SAS-Richtlinien ermöglicht). 

Um sicherzustellen, dass ein Konto nicht über Zugriffsrechte außerhalb der zugewiesenen Berechtigungen verfügt, schließen Sie beim Erstellen einer benutzerdefinierten Rolle *nicht* die Berechtigung **Microsoft.Devices/iotHubs/listkeys/action** ein. Um beispielsweise eine benutzerdefinierte Rolle zu erstellen, die Geräteidentitäten lesen, aber keine Geräte erstellen oder löschen kann, erstellen Sie eine benutzerdefinierte Rolle, für die Folgendes gilt:
- Sie verfügt über die Datenaktion **Microsoft.Devices/IotHubs/devices/read**.
- Sie verfügt nicht über die Datenaktion **Microsoft.Devices/IotHubs/devices/write**.
- Sie verfügt nicht über die Datenaktion **Microsoft.Devices/IotHubs/devices/delete**.
- Sie verfügt nicht über die Aktion **Microsoft.Devices/iotHubs/listkeys/action**.

Stellen Sie dann sicher, dass das Konto nicht über andere Rollen verfügt, die die Berechtigung **Microsoft.Devices/iotHubs/listkeys/action** einschließen, z. B. [Besitzer](../role-based-access-control/built-in-roles.md#owner) oder [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor). Weisen Sie die Rolle [Leser](../role-based-access-control/built-in-roles.md#reader) zu, damit das Konto über Ressourcenzugriff verfügt und im Portal navigieren kann.

## <a name="azure-iot-extension-for-azure-cli"></a>Azure IoT-Erweiterung für die Azure CLI

Die meisten Befehle für IoT Hub unterstützen die Azure AD-Authentifizierung. Der Authentifizierungstyp, der zum Ausführen von Befehlen verwendet wird, kann mit dem Parameter `--auth-type` gesteuert werden, der die Werte „key“ oder „login“ akzeptiert. Der Wert von `key` wird standardmäßig festgelegt.

- Wenn `--auth-type` den Wert `key` hat, ermittelt die CLI bei der Interaktion mit IoT Hub wie zuvor automatisch eine geeignete Richtlinie.

- Wenn `--auth-type` den Wert `login` hat, wird für den Vorgang ein Zugriffstoken aus dem über die Azure CLI angemeldeten Prinzipal verwendet.

Weitere Informationen finden Sie auf der [Releaseseite für die Azure IoT-Erweiterung für die Azure CLI](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12).

## <a name="sdk-samples"></a>SDK-Beispiele

- [.NET-Beispiel für das Microsoft.Azure.Devices SDK](https://aka.ms/iothubaadcsharpsample)
- [Beispiel für das Java SDK](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Vorteilen der Verwendung von Azure AD in Ihrer Anwendung finden Sie unter [Integration in Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md).
- Weitere Informationen zum Anfordern von Zugriffstoken aus Azure AD für Benutzer und Dienstprinzipale finden Sie unter [Was ist Authentifizierung?](../active-directory/develop/authentication-vs-authorization.md).
