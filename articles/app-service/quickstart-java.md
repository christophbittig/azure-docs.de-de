---
title: 'Schnellstart: Erstellen einer Java-App in Azure App Service'
description: Stellen Sie in wenigen Minuten Ihre erste Java-App vom Typ „Hallo Welt“ in Azure App Service bereit. Mit dem Azure-Web-App-Plug-In für Maven können Sie Java-Apps bequem bereitstellen.
keywords: Azure, App Service, Web-App, Windows, Linux, Java, Maven, Schnellstart
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: 09e49d2d642f80008fb5e1d0d36d4db55d56200a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307049"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Schnellstart: Erstellen einer Java-App in Azure App Service

Von [Azure App Service](overview.md) wird ein hochgradig skalierbarer Webhostingdienst mit Self-Patching bereitgestellt.  In dieser Schnellstartanleitung wird gezeigt, wie Sie mit der [Azure CLI](/cli/azure/get-started-with-azure-cli) und dem [Azure-Web-App-Plug-In für Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) eine JAR- oder WAR-Datei bereitstellen. Verwenden Sie die Registerkarten, um zwischen Java SE- und Tomcat-Anweisungen zu wechseln.

Sollte Maven nicht Ihr bevorzugtes Entwicklungstool sein, stehen ähnliche Tutorials für Java-Entwickler zur Verfügung:
+ [Gradle](./configure-language-java.md?pivots=platform-linux#gradle)
+ [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)
+ [Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)
+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-webapp)

![In Azure App Service ausgeführte Beispiel-App](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Erstellen einer Java-App

# <a name="java-se"></a>[Java SE](#tab/javase)

Klonen Sie das Beispielprojekt [Spring Boot Getting Started](https://github.com/spring-guides/gs-spring-boot).

```azurecli-interactive
git clone https://github.com/spring-guides/gs-spring-boot
```

Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt.

```azurecli-interactive
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Führen Sie den folgenden Maven-Befehl über die Eingabeaufforderung der Cloud Shell aus, um eine neue App mit dem Namen `helloworld` zu erstellen:

```azurecli-interactive
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Ändern Sie anschließend Ihr Arbeitsverzeichnis in den Projektordner:

```azurecli-interactive
cd helloworld
```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

::: zone pivot="platform-windows"

JBoss EAP ist nur in der Linux-Version von App Service verfügbar. Wählen Sie oben in diesem Artikel die Schaltfläche **Linux** aus, um die Schnellstartanweisungen für JBoss EAP anzuzeigen.

::: zone-end
::: zone pivot="platform-linux"

Klonen Sie die Demoanwendung Pet Store.

```azurecli-interactive
git clone https://github.com/agoncal/agoncal-application-petstore-ee7.git
```

Wechseln Sie in das Verzeichnis mit dem geklonten Projekt.

```azurecli-interactive
cd agoncal-application-petstore-ee7
```

::: zone-end

---

## <a name="configure-the-maven-plugin"></a>Konfigurieren des Maven-Plug-Ins

Der Prozess zur Bereitstellung in Azure App Service verwendet automatisch Ihre Azure-Anmeldeinformation aus der Azure CLI. Ist die Azure CLI nicht lokal installiert, nutzt das Maven-Plug-In OAuth oder die Geräteanmeldung für die Authentifizierung. Weitere Informationen finden Sie unter [Authentifizierung mit Maven-Plug-Ins](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Führen Sie den folgenden Maven-Befehl aus, um die Bereitstellung zu konfigurieren. Dieser Befehl unterstützt Sie beim Einrichten des App Service-Betriebssystems, der Java-Version und der Tomcat-Version.

```azurecli-interactive
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.1:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Wenn die Eingabeaufforderung für die Option **Abonnement** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
2. Wenn die Eingabeaufforderung für die Option **Web-App** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken.
3. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Windows** aus, indem Sie `1` eingeben.
4. Wenn die Eingabeaufforderung für die Option **javaVersion** angezeigt wird, wählen Sie **Java 8** aus, in dem Sie `1` eingeben.
5. Wenn die Eingabeaufforderung für die Option **Tarif** angezeigt wird, wählen Sie **P1v2** aus, in dem Sie `10` eingeben.
6. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    Die Zusammenfassungsausgabe sieht in etwa wie der unten gezeigte Codeausschnitt aus.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wenn die Eingabeaufforderung für die Option **Abonnement** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
2. Wenn die Eingabeaufforderung für die Option **Web-App** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken.
3. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Windows** aus, indem Sie `1` eingeben.
4. Wenn die Eingabeaufforderung für die Option **javaVersion** angezeigt wird, wählen Sie **Java 8** aus, in dem Sie `1` eingeben.
5. Wenn die Eingabeaufforderung für die Option **webContainer** angezeigt wird, wählen Sie **Tomcat 8.5** aus, in dem Sie `1` eingeben.
6. Wenn die Eingabeaufforderung für die Option **Tarif** angezeigt wird, wählen Sie **P1v2** aus, in dem Sie `10` eingeben.
7. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    Die Zusammenfassungsausgabe sieht in etwa wie der unten gezeigte Codeausschnitt aus.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : centralus
    PricingTier : P1v2
    OS : Windows
    Java : Java 8
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

JBoss EAP ist nur in der Linux-Version von App Service verfügbar. Wählen Sie oben in diesem Artikel die Schaltfläche **Linux** aus, um die Schnellstartanweisungen für JBoss EAP anzuzeigen.

---

::: zone-end
::: zone pivot="platform-linux"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Wenn die Eingabeaufforderung für die Option **Abonnement** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die Option **Web-App** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Linux** aus, indem Sie die EINGABETASTE drücken.
2. Wenn die Eingabeaufforderung für die Option **javaVersion** angezeigt wird, wählen Sie **Java 8** aus, in dem Sie `1` eingeben.
3. Wenn die Eingabeaufforderung für die Option **Tarif** angezeigt wird, wählen Sie **P1v2** aus, in dem Sie `9` eingeben.
4. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Java SE
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Wenn die Eingabeaufforderung für die Option **Abonnement** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn die Eingabeaufforderung für die Option **Web-App** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Linux** aus, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die Option **javaVersion** angezeigt wird, wählen Sie **Java 8** aus, in dem Sie `1` eingeben.
1. Wenn die Eingabeaufforderung für die Option **webcontainer** angezeigt wird, wählen Sie **Tomcat 8.5** aus, in dem Sie `3` eingeben.
1. Wenn die Eingabeaufforderung für die Option **Tarif** angezeigt wird, wählen Sie **P1v2** aus, in dem Sie `9` eingeben.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : centralus
    PricingTier : P1v2
    OS : Linux
    Web server stack : Tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="jboss-eap"></a>[JBoss EAP](#tab/jbosseap)

1. Wenn die Eingabeaufforderung für die Option **Abonnement** angezeigt wird, wählen Sie das richtige `Subscription` aus, indem Sie die Ziffer am Zeilenanfang eingeben.
1. Wenn eine Eingabeaufforderung für die **Web-App-Optionen** angezeigt wird, übernehmen Sie die Standardoption `<create>`, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die **Betriebssystemoption** angezeigt wird, wählen Sie **Linux** aus, indem Sie die EINGABETASTE drücken.
1. Wenn die Eingabeaufforderung für die Option **javaVersion** angezeigt wird, wählen Sie **Java 8** aus, in dem Sie `1` eingeben.
1. Wenn die Eingabeaufforderung für die Option **webContainer** angezeigt wird, wählen Sie **Jbosseap 7** aus, in dem Sie `1` eingeben.
1. Wenn die Eingabeaufforderung für die Option **pricingTier** angezeigt wird, wählen Sie **P1v3** aus, in dem Sie `1` eingeben.
1. Drücken Sie abschließend in der letzten Eingabeaufforderung die EINGABETASTE, um die Auswahl zu bestätigen.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : petstoreee7-1623451825408
    ResourceGroup : petstoreee7-1623451825408-rg
    Region : centralus
    PricingTier : P1v3
    OS : Linux
    Java : Java 8
    Web server stack: Jbosseap 7
    Deploy to slot : false
    Confirm (Y/N) [Y]: y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 01:01 min
    [INFO] Finished at: 2021-06-11T15:52:25-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Sie können bei Bedarf die Konfigurationen für App Service direkt in der Datei `pom.xml` ändern. Im Folgenden sind einige gängige Konfigurationen aufgeführt:

Eigenschaft | Erforderlich | Beschreibung | Version
---|---|---|---
`<schemaVersion>` | false | Gibt die Version des Konfigurationsschemas an. Folgende Werte werden unterstützt: `v1` und `v2`. | 1.5.2
`<subscriptionId>` | false | Geben Sie die Abonnement-ID an. | 0.1.0+
`<resourceGroup>` | true | Azure-Ressourcengruppe für Ihre Web-App. | 0.1.0+
`<appName>` | true | Der Name Ihrer Web-App. | 0.1.0+
`<region>` | false | Gibt die Region an, in der Ihre Web-App gehostet wird. Der Standardwert ist **centralus**. Alle gültigen Regionen finden Sie im Abschnitt [Unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=app-service). | 0.1.0+
`<pricingTier>` | false | Der Tarif für Ihre Web-App. Für Produktionsworkloads lautet der Standardwert **P1v2**, für Entwicklungs- und Testworkloads in Java ist der empfohlene Mindestwert hingegen **B2**. [Weitere Informationen](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0+
`<runtime>` | false | Details zur Konfiguration der Laufzeitumgebung finden Sie [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+
`<deployment>` | false | Details zur Konfiguration der Bereitstellung finden Sie [hier](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0+

Notieren Sie sich die Werte `<appName>` und `<resourceGroup>` (`helloworld-1590394316693` und `helloworld-1590394316693-rg` in der Demo). Sie werden später verwendet.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Bereitstellen der App

Wenn alle Konfigurationseinstellungen in Ihrer POM-Datei eingerichtet sind, können Sie Ihre Java-App mit einem einzigen Befehl in Azure bereitstellen.

```azurecli-interactive
mvn package azure-webapp:deploy
```

::: zone pivot="platform-linux"

> [!NOTE]
> Führen Sie für JBoss EAP `mvn package azure-webapp:deploy -DskipTests` aus, um Tests zu deaktivieren, da Wildfly lokal installiert sein muss. 

::: zone-end

Nach Abschluss der Bereitstellung steht Ihre Anwendung unter `http://<appName>.azurewebsites.net/` (`http://helloworld-1590394316693.azurewebsites.net` in der Demo) bereit. Öffnen Sie die URL in Ihrem lokalen Webbrowser. Folgendes sollte angezeigt werden:

![In Azure App Service ausgeführte Beispiel-App](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Glückwunsch!** Sie haben Ihre erste Java-App in App Service bereitgestellt.

> [!div class="nextstepaction"]
> [Ich bin auf ein Problem gestoßen](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe im Portal, indem Sie den folgenden Befehl in Cloud Shell ausführen:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Verwenden von Java zum Herstellen einer Verbindung mit einem Azure Database for PostgreSQL-Einzelserver und zum Abfragen von Daten](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Richten Sie CI/CD ein.](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Preisinformationen](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Aggregieren von Protokollen und Metriken](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Hochskalieren](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Ressourcen: Azure für Java-Entwickler](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurieren der Java-App](configure-language-java.md)
