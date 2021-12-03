---
title: Zugreifen auf Config Server und Service Registry
titleSuffix: Azure Spring Cloud
description: Zugreifen auf Config Server- und Service Registry-Endpunkte mit der rollenbasierten Zugriffssteuerung in Azure Active Directory
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/25/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: 9c4a5715af9fe98f5e577410219a54368c27b6df
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054873"
---
# <a name="access-config-server-and-service-registry"></a>Zugreifen auf Config Server und Service Registry

In diesem Artikel wird erläutert, wie Sie mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) auf Spring Cloud Config Server und Spring Cloud Service Registry zugreifen, die von Azure Spring Cloud verwaltet werden.

> [!NOTE]
> Anwendungen, die im Azure Spring Cloud-Dienst bereitgestellt und ausgeführt werden, werden automatisch mit der zertifikatbasierten Authentifizierung und Autorisierung vernetzt, wenn sie auf den verwalteten Spring Cloud-Konfigurationsserver (Config Server) und die -Dienstregistrierung zugreifen. Sie müssen diese Anleitung für diese Anwendungen nicht befolgen. Die zugehörigen Zertifikate werden vollständig von der Azure Spring Cloud-Plattform verwaltet und automatisch in Ihre Anwendung eingefügt, wenn eine Verbindung mit Config Server und der Dienstregistrierung besteht.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Zuweisen einer Rolle zu einem Azure AD-Benutzer bzw. einer Azure AD-Gruppe, einem MSI oder Dienstprinzipal

Weisen Sie die Rolle dem/r [user | group | service-principal | managed-identity] mit dem Umfang [management-group | subscription | resource-group | resource] zu.

| Rollenname                                       | BESCHREIBUNG                                                                  |
|-------------------------------------------------|------------------------------------------------------------------------------|
| Azure Spring Cloud Config Server-Leser         | Gewährt Lesezugriff auf den Azure Spring Cloud Config Server.                       |
| Azure Spring Cloud Config Server-Mitwirkender    | Gewährt Lese-, Schreib- und Löschzugriff auf den Azure Spring Cloud Config Server.    |
| Azure Spring Cloud Service Registry-Leser      | Gewährt Lesezugriff auf die Azure Spring Cloud Service Registry.                    |
| Azure Spring Cloud Service Registry-Mitwirkender | Gewährt Lese-, Schreib- und Löschzugriff auf die Azure Spring Cloud Service Registry. |

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="access-config-server-and-service-registry-endpoints"></a>Zugreifen auf Config Server- und Service Registry-Endpunkte

Nach dem Zuweisen der Rolle kann deren Empfänger auf die Spring Cloud Config Server- und Spring Cloud Service Registry-Endpunkte mithilfe des folgenden Verfahrens zugreifen:

1. Rufen Sie ein Zugriffstoken ab. Nachdem einem Azure AD-Benutzer die Rolle zugewiesen wurde, kann er sich mithilfe der folgenden Befehle bei der Azure CLI mit Benutzeranmeldeinformationen, einem Dienstprinzipal oder einer verwalteten Identität anmelden, um ein Zugriffstoken zu erhalten. Ausführliche Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

    ```azurecli
    az login
    az account get-access-token
    ```

1. Erstellen Sie den Endpunkt. Wir unterstützen die Standardendpunkte von Spring Cloud Config Server und Spring Cloud Service Registry, die von Azure Spring Cloud verwaltet werden.

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/{path}'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/{path}'*

    >[!NOTE]
    > Wenn Sie Azure China verwenden, ersetzen Sie `*.azuremicroservices.io` durch `*.microservices.azure.cn`. Weitere Informationen finden Sie im Abschnitt [Überprüfen von Endpunkten in Azure](/azure/china/resources-developer-guide#check-endpoints-in-azure) im [Azure China-Entwicklerhandbuch](/azure/china/resources-developer-guide).

1. Greifen Sie mit dem Zugriffstoken auf den erstellten Endpunkt zu. Fügen Sie das Zugriffstoken in einen Header ein, um die Autorisierung zu ermöglichen: `--header 'Authorization: Bearer {TOKEN_FROM_PREVIOUS_STEP}`.

    Beispiel:

    a. Greifen Sie auf einen Endpunkt wie *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/health '* zu, um den Integritätsstatus von Config Server anzuzeigen.

    b. Greifen Sie auf einen Endpunkt wie *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/eureka/apps '* zu, um die registrierten Apps in Spring Cloud Service Registry (in diesem Fall Eureka) anzuzeigen.

    Wenn die Antwort *401 Unauthorized* (401 Nicht autorisiert) ist, überprüfen Sie, ob die Rolle erfolgreich zugewiesen wurde.  Es dauert einige Minuten, bis die Rolle wirksam wird. Sie können auch überprüfen, ob das Zugriffstoken abgelaufen ist.

Weitere Informationen zum Aktuatorendpunkt finden Sie unter [Produktionsbereite Endpunkte](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints).

Informationen zu Eureka-Endpunkten finden Sie unter [Eureka-REST-Vorgänge](https://github.com/Netflix/eureka/wiki/Eureka-REST-operations).

Informationen zu Konfigurationsserverendpunkten und ausführlichen Pfadinformationen finden Sie unter [ResourceController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/resource/ResourceController.java) und [EncryptionController.java](https://github.com/spring-cloud/spring-cloud-config/blob/main/spring-cloud-config-server/src/main/java/org/springframework/cloud/config/server/encryption/EncryptionController.java).

## <a name="register-spring-boot-apps-to-spring-cloud-config-server-and-service-registry-managed-by-azure-spring-cloud"></a>Registrieren von Spring Boot-Apps bei Spring Cloud Config Server und der Service Registry, die von Azure Spring Cloud verwaltet werden

Nachdem die Rolle zugewiesen wurde, können Sie Spring Boot-Apps bei Spring Cloud Config Server und der Service Registry registrieren, die von Azure Spring Cloud mit Azure AD-Tokenauthentifizierung verwaltet werden. Sowohl Config Server als auch die Service Registry unterstützen [benutzerdefinierte REST-Vorlagen](https://cloud.spring.io/spring-cloud-config/reference/html/#custom-rest-template) zum Einfügen des Bearertokens für die Authentifizierung.

Weitere Informationen finden Sie in den Beispielen [Zugreifen auf den von Azure Spring Cloud verwalteten Config Server](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client) und [Zugreifen auf die von Access Azure Spring Cloud verwaltete Spring Cloud Service Registry](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client). In den folgenden Abschnitten werden einige wichtige Details in diesen Beispielen erläutert.

**In *AccessTokenManager.java*:**

`AccessTokenManager` ist für das Abrufen eines Zugriffstokens aus Azure AD verantwortlich. Konfigurieren Sie die Anmeldeinformationen des Dienstprinzipals in der Datei *application.properties*, und initialisieren Sie `ApplicationTokenCredentials`, um das Token abzurufen. Sie finden diese Datei in beiden Beispielen.

```java
prop.load(in);
tokenClientId = prop.getProperty("access.token.clientId");
String tenantId = prop.getProperty("access.token.tenantId");
String secret = prop.getProperty("access.token.secret");
String clientId = prop.getProperty("access.token.clientId");
credentials = new ApplicationTokenCredentials(
    clientId, tenantId, secret, AzureEnvironment.AZURE);
```

**In *CustomConfigServiceBootstrapConfiguration.java*:**

`CustomConfigServiceBootstrapConfiguration` implementiert die benutzerdefinierte REST-Vorlage für Config Server und fügt das Token aus Azure AD als `Authorization`-Header ein. Sie finden diese Datei im [Config Server-Beispiel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-config-server-client).

```java
public class RequestResponseHandlerInterceptor implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) throws IOException {
        String accessToken = AccessTokenManager.getToken();
        request.getHeaders().remove(AUTHORIZATION);
        request.getHeaders().add(AUTHORIZATION, "Bearer " + accessToken);

        ClientHttpResponse response = execution.execute(request, body);
        return response;
    }

}
```

**In *CustomRestTemplateTransportClientFactories.java*:**

Die vorherigen beiden Klassen dienen zur Implementierung der benutzerdefinierten REST-Vorlage für Spring Cloud Service Registry. Der `intercept`-Teil ist mit dem in Config Server weiter oben identisch. Achten Sie darauf, den Nachrichtenkonvertern `factory.mappingJacksonHttpMessageConverter()` hinzuzufügen. Sie finden diese Datei im [Spring Cloud Service Registry-Beispiel](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/custom-eureka-client).

```java
private RestTemplate customRestTemplate() {
    /*
     * Inject your custom rest template
     */
    RestTemplate restTemplate = new RestTemplate();
    restTemplate.getInterceptors()
        .add(new RequestResponseHandlerInterceptor());
    RestTemplateTransportClientFactory factory = new RestTemplateTransportClientFactory();

    restTemplate.getMessageConverters().add(0, factory.mappingJacksonHttpMessageConverter());

    return restTemplate;
}
```

Wenn Sie Anwendungen in einem Kubernetes-Cluster ausführen, wird empfohlen, eine IP-Adresse zu verwenden, um Spring Cloud Service Registry für den Zugriff zu registrieren.

```properties
eureka.instance.prefer-ip-address=true
```

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizieren der Azure CLI](/cli/azure/authenticate-azure-cli)
* [Produktionsbereite Endpunkte](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)
* [Erstellen von Rollen und Berechtigungen](how-to-permissions.md)
