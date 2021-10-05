---
title: Kontrolle des Zugriffs auf loT Hub mit Azure Active Directory
description: Leitfaden für Developer. Steuerung des Zugriffs auf loT Hub für Back-End-Apps mithilfe von Azure AD und Azure RBAC.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: a8387a5732ab51add02495b03236b42f9cd4e671
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128609439"
---
# <a name="control-access-to-iot-hub-by-using-azure-active-directory"></a>Kontrolle des Zugriffs auf loT Hub mit Azure Active Directory

Sie können Azure Active Directory (Azure AD) verwenden, um Anforderungen an Azure loT Hub Service-APIs zu authentifizieren, z.B. Geräteidentität erstellen und direkte Methode aufrufen. Sie können auch Azure rollenbasierte Zugriffskontrolle (Azure RBAC) verwenden, um dieselben Service-APIs zu autorisieren. Wenn Sie diese Technologien zusammen verwenden, können Sie einem Azure AD-Sicherheitsprinzipal Berechtigungen für den Zugriff auf loT Hub-Service-APIs erteilen. Dieser Sicherheitsprinzipal kann ein Benutzer, eine Gruppe oder ein Anwendungsdienstprinzipal sein.

Die Authentifizierung des Zugriffs mithilfe von Azure AD und die Kontrolle der Berechtigungen mithilfe von Azure RBAC bieten eine verbesserte Sicherheit und Benutzerfreundlichkeit im Vergleich zu [Sicherheitstokens](iot-hub-dev-guide-sas.md). Um mögliche Sicherheitsprobleme im Zusammenhang mit Sicherheitstokens zu minimieren, empfehlen wir, dass Sie [wann immer möglich Azure AD mit Ihrem loT-Hub verwenden](#azure-ad-access-and-shared-access-policies). 

> [!NOTE]
> Die Authentifizierung mit Azure AD wird für die *Geräte-APIs* des loT-Hubs (wie Geräte-zu-Cloud-Nachrichten und Aktualisierung gemeldeter Eigenschaften) nicht unterstützt. Verwenden Sie [symmetrische Schlüssel](iot-hub-dev-guide-sas.md#use-a-symmetric-key-in-the-identity-registry) oder [X.509](iot-hub-x509ca-overview.md), um Geräte am loT Hub zu authentifizieren.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Wenn ein Azure AD-Sicherheitsprinzipal den Zugriff auf eine loT Hub-Service-API anfordert, wird die Identität des Prinzipals zunächst *authentifiziert*. Zur Authentifizierung muss die Anforderung zur Ausführung ein OAuth 2.0-Zugangs-Token enthalten. Der Ressourcenname zum Anfordern des Tokens lautet `https://iothubs.azure.net`. Wenn die Anwendung in einer Azure-Ressource wie einer Azure-VM, einer Azure Functions App oder einer Azure App Service App ausgeführt wird, kann sie als [verwaltete Identität dargestellt werden](../active-directory/managed-identities-azure-resources/how-managed-identities-work-vm.md). 

Nachdem der Azure AD-Prinzipal authentifiziert wurde, ist der nächste Schritt die *Autorisierung*. In diesem Schritt verwendet loT Hub den Azure AD Rollenzuweisungsdienst, um festzustellen, welche Berechtigungen der Prinzipal hat. Wenn die Berechtigungen des Prinzipals für die angeforderte Ressource oder API ausreichen, autorisiert IoT Hub die Anforderung. Dieser Schritt erfordert also, dass dem Sicherheitsprinzipal eine oder mehrere Azure-Rollen zugewiesen werden. IoT Hub bietet einige integrierte Rollen, die allgemeine Berechtigungsgruppen umfassen.

## <a name="manage-access-to-iot-hub-by-using-azure-rbac-role-assignment"></a>Verwalten des Zugriffs auf Azure IoT Hub mithilfe der Azure RBAC-Rollenzuweisung

Bei Azure AD und Azure RBAC erfordert IoT Hub, dass der Prinzipal, der die API anfordert, über eine ausreichende Berechtigungsebene für die Autorisierung verfügt. Um dem Prinzipal die Berechtigung zu erteilen, weisen Sie ihm eine Rolle zu. 

- Wenn es sich bei dem Prinzipal um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handelt, befolgen Sie die Anleitung in [Zuweisen von Azure Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
- Wenn es sich bei dem Prinzipal um eine verwaltete Identität handelt, befolgen Sie die Anleitung unter [Zuweisung vom Zugriff einer verwalteten Identität auf eine Ressource über das Azure-Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

Um das geringste Privileg zu gewährleisten, weisen Sie die entsprechende Rolle immer dem niedrigstmöglichen [Ressourcenbereich](#resource-scope) zu, der wahrscheinlich der Azure loT Hub Bereich ist.

loT Hub bietet die folgenden in Azure integrierten Rollen für die Autorisierung des Zugriffs auf loT Hub-Service-APIs unter Verwendung von Azure AD und RBAC:

| Rolle | BESCHREIBUNG | 
| ---- | ----------- | 
| [Mitwirkender an IoT Hub-Daten](../role-based-access-control/built-in-roles.md#iot-hub-data-contributor) | Ermöglicht den vollen Zugriff auf Azure loT Hub Datenebenen-Vorgänge. |
| [IoT Hub-Datenleser](../role-based-access-control/built-in-roles.md#iot-hub-data-reader) | Ermöglicht vollen Lesezugriff auf Eigenschaften der Azure loT Hub-Datenebene. |
| [Mitwirkender an IoT Hub-Registrierung](../role-based-access-control/built-in-roles.md#iot-hub-registry-contributor) | Ermöglicht vollen Zugriff auf die Azure loT Hub-Geräteregistrierung. |
| [Mitwirkender an IoT Hub-Zwillingen](../role-based-access-control/built-in-roles.md#iot-hub-twin-contributor) | Erlaubt Lese- und Schreibzugriff auf alle Azure loT Hub-Geräte- und -Modulzwillinge. |

Sie können auch benutzerdefinierte Rollen für die Verwendung mit Azure loT Hub definieren, indem Sie die benötigten [Berechtigungen](#permissions-for-iot-hub-service-apis) kombinieren. Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten Rollen für die rollenbasierte Zugriffskontrolle in Azure](../role-based-access-control/custom-roles.md).

### <a name="resource-scope"></a>Ressourcenumfang

Bevor Sie einem Sicherheitsprinzipal eine Azure RBAC-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es ist immer am besten, nur den kleinstmöglichen Bereich zu gewähren. Azure RBAC-Rollen, die in einem umfassenderen Bereich definiert sind, werden von den darunterliegenden Ressourcen geerbt.

Diese Liste beschreibt die Ebenen, auf denen Sie den Zugriff auf Azure loT Hub einschränken können, angefangen mit dem engsten Bereich:

- **Der IoT-Hub:** In diesem Bereich gilt eine Rollenzuweisung für den Azure loT Hub. Es gibt keinen kleineren Bereich als einen einzelnen IoT-Hub. Rollenzuweisung in kleineren Bereichen, wie individuelle Geräteidentität oder Zwillingsabschnitt, wird nicht unterstützt.
- **Die Ressourcengruppe**. In diesem Bereich gilt eine Rollenzuweisung für alle IoT Hubs in der Ressourcengruppe.
- **Das Abonnement**. In diesem Bereich gilt eine Rollenzuweisung für alle IoT Hubs in allen Ressourcengruppen im Abonnement.
- **Eine Verwaltungsgruppe**. In diesem Umfang gilt eine Rollenzuweisung für alle loT Hubs in allen Ressourcengruppen in allen Abonnements in der Verwaltungsgruppe.

## <a name="permissions-for-iot-hub-service-apis"></a>Berechtigungen für Azure loT Hub-Service-APIs

Die folgende Tabelle beschreibt die Berechtigungen, die für Azure loT Hub-Service-API-Vorgänge verfügbar sind. Um einem Client den Aufruf eines bestimmten Vorgangs zu ermöglichen, stellen Sie sicher, dass die zugewiesene RBAC-Rolle des Clients ausreichende Berechtigungen für den Vorgang bietet.

| RBAC-Aktion | BESCHREIBUNG |
|-|-|
| `Microsoft.Devices/IotHubs/devices/read` | Lesen einer beliebigen Geräte- oder Modulidentität. |
| `Microsoft.Devices/IotHubs/devices/write`  | Erstellen oder Aktualisieren einer beliebigen Geräte- oder Modulidentität.  |
| `Microsoft.Devices/IotHubs/devices/delete` | Löschen einer beliebigen Geräte- oder Modulidentität. |
| `Microsoft.Devices/IotHubs/twins/read` | Lesen eines beliebigen Geräte- oder Modulzwillings. |
| `Microsoft.Devices/IotHubs/twins/write` | Schreiben in alle Geräte oder Modulzwillinge. |
| `Microsoft.Devices/IotHubs/jobs/read` | Zurückgeben von einer Liste von Aufträgen. |
| `Microsoft.Devices/IotHubs/jobs/write` | Erstellen oder aktualisieren von allen Aufträgen. |
| `Microsoft.Devices/IotHubs/jobs/delete` | Löschen vom irgendeinem Auftrag. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/send/action` | Senden einer Cloud-to-Gerät-Nachricht an ein beliebiges Gerät.  |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/feedback/action` | Empfangen, abschließen oder abbrechen von einer Rückmeldung über eine Cloud-to-Gerät-Nachricht. |
| `Microsoft.Devices/IotHubs/cloudToDeviceMessages/queue/purge/action` | Löschen von allen ausstehenden Befehlen für ein Gerät.  |
| `Microsoft.Devices/IotHubs/directMethods/invoke/action` | Aufrufen von einer direkten Methode auf einem beliebigen Gerät oder Modul. |
| `Microsoft.Devices/IotHubs/fileUpload/notifications/action`  | Erhalten, Abschließen oder Abbrechen von Datei-Upload-Benachrichtigungen. |
| `Microsoft.Devices/IotHubs/statistics/read` | Lesen von Geräte- und Dienststatistiken. |
| `Microsoft.Devices/IotHubs/configurations/read` | Lesen von Geräteverwaltungskonfigurationen. |
| `Microsoft.Devices/IotHubs/configurations/write` | Erstellen oder Aktualisieren von Geräteverwaltungskonfigurationen. |
| `Microsoft.Devices/IotHubs/configurations/delete` | Löschen von beliebigen Geräteverwaltungskonfigurationen. |
| `Microsoft.Devices/IotHubs/configurations/applyToEdgeDevice/action`  | Anwenden des Konfigurationsinhalts auf ein Edgegerät. |
| `Microsoft.Devices/IotHubs/configurations/testQueries/action` | Validieren der Zielbedingung und der benutzerdefinierten Metrikabfragen für eine Konfiguration. |

> [!TIP]
> - Für den Vorgang der [Aktualisierung der Massenregistrierung (Bulk Registry Update)](/rest/api/iothub/service/bulkregistry/updateregistry) sind sowohl `Microsoft.Devices/IotHubs/devices/write` als auch `Microsoft.Devices/IotHubs/devices/delete` erforderlich.
> - Für den Vorgang [Zwillingsabfrage](/rest/api/iothub/service/query/gettwins) ist `Microsoft.Devices/IotHubs/twins/read` erforderlich.
> - [Get Digital Twin](/rest/api/iothub/service/digitaltwin/getdigitaltwin) (Digitalen Zwilling anfordern) erfordert `Microsoft.Devices/IotHubs/twins/read`. [Update Digital Twin](/rest/api/iothub/service/digitaltwin/updatedigitaltwin)(Digitalen Zwilling aktualisieren) erfordert `Microsoft.Devices/IotHubs/twins/write`.
> - Sowohl das [Aufrufen eines Komponentenbefehls](/rest/api/iothub/service/digitaltwin/invokecomponentcommand) als auch das [Aufrufen eines Befehls auf Stammebene](/rest/api/iothub/service/digitaltwin/invokerootlevelcommand) erfordern `Microsoft.Devices/IotHubs/directMethods/invoke/action`.

> [!NOTE]
> Um Daten von loT Hub über Azure AD abzurufen, [richten Sie ein Routing zu einem separaten Event Hub ein](iot-hub-devguide-messages-d2c.md#event-hubs-as-a-routing-endpoint). Für den Zugriff auf den [eingebauten Azure Event Hubs kompatiblen Endpunkt](iot-hub-devguide-messages-read-builtin.md) verwenden Sie die Methode der Verbindungszeichenfolge (gemeinsamer Zugriffsschlüssel) wie zuvor. 

## <a name="azure-ad-access-and-shared-access-policies"></a>Zugriffs- und freigegebene Zugriffsrichtlinien mit Azure AD

Standardmäßig unterstützt loT Hub den Service-API-Zugriff sowohl über Azure AD als auch über [freigegebene Zugriffsrichtlinien und Sicherheitstoken](iot-hub-dev-guide-sas.md). Deaktivieren Sie den Zugriff über freigegebene Zugriffsrichtlinien, um die potenziellen Sicherheitsrisiken bei Sicherheitstoken zu minimieren: 

1. Stellen Sie sicher, dass Ihre Dienstclients und Benutzer [ausreichenden Zugang](#manage-access-to-iot-hub-by-using-azure-rbac-role-assignment) zu Ihrem loT Hub haben. Befolgen Sie das [Prinzip der geringsten Privilegien](../security/fundamentals/identity-management-best-practices.md).
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT Hub.
1. Wählen Sie im linken Bereich **freigegebene Zugriffsrichtlinien**.
1. Wählen Sie unter **Verbindung unter Verwendung freigegebener Zugriffsrichtlinien herstellen** die Option **Verweigern** aus.
    :::image type="content" source="media/iot-hub-dev-guide-azure-ad-rbac/disable-local-auth.png" alt-text="Screenshot, der zeigt, wie Sie die freigegebenen Zugriffsrichtlinien von Azure loT Hub deaktivieren.":::
1. Überprüfen Sie die Warnung, und wählen Sie dann **Speichern**.

Auf Ihre loT Hub-Service-APIs kann jetzt nur noch über Azure AD und RBAC zugegriffen werden.

## <a name="azure-ad-access-from-the-azure-portal"></a>Azure AD-Zugriff über das Azure-Portal

Wenn Sie versuchen, auf loT Hub zuzugreifen, prüft das Azure-Portal zunächst, ob Ihnen eine Azure-Rolle mit `Microsoft.Devices/iotHubs/listkeys/action` zugewiesen wurde. Wenn ja, verwendet das Azure-Portal die Schlüssel aus den freigegebenen Zugriffsrichtlinien, um auf loT Hub zuzugreifen. Wenn nicht, versucht das Azure-Portal, über Ihr Azure AD-Konto auf die Daten zuzugreifen. 

Um über Ihr Azure AD-Konto vom Azure-Portal aus auf loT Hub zuzugreifen, benötigen Sie Berechtigungen für den Zugriff auf loT Hub-Datenressourcen (wie Geräte und Zwillinge). Sie benötigen auch Berechtigungen, um die loT Hub-Ressource im Azure-Portal aufzurufen. Die von Azure loT Hub bereitgestellten integrierten Rollen gewähren Zugriff auf Ressourcen wie Geräte und Zwillinge. Aber sie gewähren keinen Zugriff auf die Azure loT Hub-Ressource. Der Zugriff auf das Portal erfordert daher auch die Zuweisung einer Azure Resource Manager-Rolle wie [Leser](../role-based-access-control/built-in-roles.md#reader). Die Leser-Rolle ist eine gute Wahl, da sie die am meisten eingeschränkte Rolle ist, mit der Sie im Portal navigieren können. Sie beinhaltet nicht die Berechtigung `Microsoft.Devices/iotHubs/listkeys/action` (die den Zugriff auf alle Azure loT Hub-Datenressourcen über geteilte Zugriffsrichtlinien ermöglicht). 

Um sicherzustellen, dass ein Konto keinen Zugriff außerhalb der zugewiesenen Berechtigungen hat, sollten Sie die Berechtigung `Microsoft.Devices/iotHubs/listkeys/action` nicht einschließen, wenn Sie eine benutzerdefinierte Rolle erstellen. Um beispielsweise eine benutzerdefinierte Rolle zu erstellen, die Geräteidentitäten lesen, aber keine Geräte erstellen oder löschen kann, erstellen Sie eine benutzerdefinierte Rolle, die:
- Über die Datenaktion `Microsoft.Devices/IotHubs/devices/read` verfügt.
- Nicht über die Datenaktion `Microsoft.Devices/IotHubs/devices/write` verfügt.
- Nicht über die Datenaktion `Microsoft.Devices/IotHubs/devices/delete` verfügt.
- Nicht über die Aktion `Microsoft.Devices/iotHubs/listkeys/action` verfügt.

Vergewissern Sie sich dann, dass das Konto keine anderen Rollen mit der Berechtigung `Microsoft.Devices/iotHubs/listkeys/action` hat, z. B. [Eigentümer](../role-based-access-control/built-in-roles.md#owner) oder [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor). Um dem Konto den Ressourcenzugriff zu ermöglichen und im Portal zu navigieren, weisen Sie [Leser](../role-based-access-control/built-in-roles.md#reader) zu.

## <a name="azure-iot-extension-for-azure-cli"></a>Azure IoT-Erweiterung für die Azure CLI

Die meisten Befehle für IoT Hub unterstützen die Azure AD-Authentifizierung. Sie können die Art der Authentifizierung, die für die Ausführung von Befehlen verwendet wird, mit dem Parameter `--auth-type` steuern, der die Werte `key` oder `login` akzeptiert. Der Wert `key` ist der standardmäßige Wert.

- Wenn `--auth-type` den Wert `key` hat, findet die Befehlszeilenschnittstelle wie zuvor automatisch eine geeignete Richtlinie, wenn sie mit Azure loTHub interagiert.

- Wenn `--auth-type` den Wert `login` hat, wird ein Zugriffstoken von der im Prinzipal angemeldeten Azure CLI für den Vorgang verwendet.

Weitere Informationen finden Sie in der [Azure loT Erweiterung für Azure CLI Release Seite](https://github.com/Azure/azure-iot-cli-extension/releases/tag/v0.10.12).

## <a name="sdk-samples"></a>SDK-Beispiele

- [.NET-Beispiel für das Microsoft.Azure.Devices SDK](https://aka.ms/iothubaadcsharpsample)
- [Beispiel für das Java SDK](https://aka.ms/iothubaadjavasample)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu den Vorteilen der Verwendung von Azure AD in Ihrer Anwendung finden Sie unter [Integration in Azure Active Directory](../active-directory/develop/active-directory-how-to-integrate.md).
- Weitere Informationen zum Anfordern von Zugriffstoken aus Azure AD für Benutzer und Dienstprinzipale finden Sie unter [Was ist Authentifizierung?](../active-directory/develop/authentication-vs-authorization.md).
