---
title: Verwenden der dynamischen Konfiguration in einer Spring Boot-App
titleSuffix: Azure App Configuration
description: Hier erfahren Sie, wie Sie Konfigurationsdaten für Spring Boot-Apps dynamisch aktualisieren.
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 34225576d435f9ff275f74372afa2cc9f7d50b9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287246"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Verwenden der dynamischen Konfiguration in einer Java Spring-App

App Configuration verfügt über zwei Bibliotheken für Spring. `azure-spring-cloud-appconfiguration-config` erfordert Spring Boot und ist abhängig von `spring-cloud-context`. `azure-spring-cloud-appconfiguration-config-web` erfordert Spring Web sowie Spring Boot. Beide Bibliotheken unterstützen die manuelle Auslösung, um nach aktualisierten Konfigurationswerten zu suchen. `azure-spring-cloud-appconfiguration-config-web` fügt darüber hinaus Unterstützung für die automatische Überprüfung der Konfigurationsaktualisierung hinzu.

Mithilfe der Aktualisierung können Sie Ihre Konfigurationswerte aktualisieren, ohne die Anwendung neu starten zu müssen. Dadurch werden jedoch alle Beans in `@RefreshScope` neu erstellt. Die Clientbibliothek speichert eine Hash-ID der zurzeit geladenen Konfigurationen zwischen, um zu viele Aufrufe des Konfigurationsspeichers zu vermeiden. Der Wert wird vom Aktualisierungsvorgang erst aktualisiert, wenn der zwischengespeicherte Wert abgelaufen ist. Dies gilt auch, wenn sich der Wert im Konfigurationsspeicher geändert hat. Die Standardablaufzeit für jede Anforderung beträgt 30 Sekunden. Sie kann bei Bedarf außer Kraft gesetzt werden.

Die automatisierte Aktualisierung von `azure-spring-cloud-appconfiguration-config-web` wird basierend auf der Aktivität (insbesondere `ServletRequestHandledEvent` von Spring Web) ausgelöst. Wird `ServletRequestHandledEvent` nicht ausgelöst, löst die automatisierte Aktualisierung von `azure-spring-cloud-appconfiguration-config-web` keine Aktualisierung aus, auch wenn die Cacheablaufzeit verstrichen ist.

## <a name="use-manual-refresh"></a>Verwenden der manuellen Aktualisierung

In App Configuration wird das `AppConfigurationRefresh`-Element verfügbar gemacht. Damit kann überprüft werden, ob der Cache abgelaufen ist, und bei Ablauf eine Aktualisierung ausgelöst werden.

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`refreshConfigurations()` von `AppConfigurationRefresh` gibt ein `Future`-Element zurück. Dieses ist „true“, wenn eine Aktualisierung ausgelöst wurde, und „false“, wenn keine Aktualisierung ausgelöst wurde. „false“ bedeutet, dass die Ablaufzeit des Caches nicht verstrichen ist, keine Änderung vorgenommen wurde oder ein anderer Thread zurzeit auf eine Aktualisierung prüft.

## <a name="use-automated-refresh"></a>Verwenden der automatisierten Aktualisierung

Wenn Sie die automatisierte Aktualisierung verwenden möchten, beginnen Sie mit einer Spring Boot-App, die App Configuration nutzt. Dies kann beispielsweise die App sein, die Sie durch Ausführen der Anleitung unter [Schnellstart: Erstellen einer Java Spring-App mit Azure App Configuration](quickstart-java-spring-app.md) erstellen.

Öffnen Sie dann die Datei *pom.xml* in einem Text-Editor, und fügen Sie unter Verwendung des folgenden Codes eine `<dependency>` für `azure-spring-cloud-appconfiguration-config-web` hinzu.

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> Unterstützung für ältere Abhängigkeiten finden Sie in unserer [vorherige Bibliothek](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md).

1. Aktualisieren von `bootstrap.properties` zum Aktivieren der Aktualisierung

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. Öffnen Sie das **Azure-Portal**, und navigieren Sie zu der App Configuration-Ressource, die Ihrer Anwendung zugeordnet ist. Wählen Sie **Konfigurations-Explorer** unter **Vorgänge** aus, und erstellen Sie durch Auswählen von **+ Erstellen** > **Schlüssel-Wert** ein neues Schlüssel-Wert-Paar, um die folgenden Parameter hinzuzufügen:

    | Schlüssel | Wert |
    |---|---|
    | Sentinel | 1 |

    Lassen Sie **Bezeichnung** und **Inhaltstyp** vorerst leer.

1. Wählen Sie **Übernehmen**.

1. Erstellen Sie Ihre Spring Boot-Anwendung mit Maven, und führen Sie sie aus.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Öffnen Sie ein neues Browserfenster, und navigieren Sie zur URL `http://localhost:8080`.  Die Nachricht für Ihren Schlüssel wird angezeigt.

    Sie können auch *curl* zum Testen Ihrer Anwendung verwenden. Beispiel:

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Öffnen Sie zum Testen der dynamischen Konfiguration das Azure App Configuration-Portal, das Ihrer Anwendung zugeordnet ist. Wählen Sie den **Konfigurations-Explorer** aus, und aktualisieren Sie den Wert des angezeigten Schlüssels. Beispiel:

    | Schlüssel | Wert |
    |---|---|
    | /application/config.message | Hello - Updated |

1. Aktualisieren Sie den zuvor erstellten Sentinel-Schlüssel auf einen neuen Wert. Durch diese Änderung wird die Anwendung angewiesen, alle Konfigurationsschlüssel zu aktualisieren, nachdem das Aktualisierungsintervall abgelaufen ist.

    | Schlüssel | Wert |
    |---|---|
    | Sentinel | 2 |

1. Aktualisieren Sie die Browserseite, um die neue Nachricht anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre Spring Boot-App aktiviert, um Konfigurationseinstellungen dynamisch aus App Configuration zu aktualisieren. Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie eine von Azure verwaltete Identität hinzufügen, um den Zugriff auf App Configuration zu optimieren.

> [!div class="nextstepaction"]
> [Integration der verwalteten Identität](./howto-integrate-azure-managed-service-identity.md)
