---
title: Konfigurieren von Java-Apps
description: Informationen zum Konfigurieren von Java-Apps zur Ausführung in Azure App Service. In diesem Artikel werden die gängigsten Konfigurationsaufgaben vorgestellt.
keywords: Azure App Service, Web-App, Windows, OSS, Java, Tomcat, JBoss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java, devx-track-azurecli
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
ms.openlocfilehash: bd98cd6a0317400bcae932d9f08f719cb8c7fc0f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558913"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurieren einer Java-App für Azure App Service

Mit Azure App Service können Java-Entwickler ihre Java SE-, Tomcat- und JBoss EAP-Webanwendungen in einem vollständig verwalteten Dienst schnell erstellen, bereitstellen und skalieren. Stellen Sie Anwendungen mit Maven-Plug-Ins über die Befehlszeile oder in Editoren wie IntelliJ, Eclipse oder Visual Studio Code bereit.

Dieser Leitfaden enthält die wichtigsten Konzepte und Anweisungen für Java-Entwickler, die App Service verwenden. Wenn Sie Azure App Service noch nie verwendet haben, lesen Sie zunächst den [Java-Schnellstart](quickstart-java.md). Allgemeine Fragen zur Verwendung von App Service, die sich nicht speziell auf die Java-Entwicklung beziehen, werden unter [Häufig gestellte Fragen (FAQ) zu App Service](faq-configuration-and-management.yml) beantwortet.

## <a name="show-java-version"></a>Java-Version anzeigen

::: zone pivot="platform-windows"  

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle Java-Version anzuzeigen:

```azurecli-interactive
az webapp config show --name <app-name> --resource-group <resource-group-name> --query "[javaVersion, javaContainer, javaContainerVersion]"
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Java-Versionen anzuzeigen:

```azurecli-interactive
az webapp list-runtimes | grep java
```

::: zone-end

::: zone pivot="platform-linux"

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um die aktuelle Java-Version anzuzeigen:

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Führen Sie in [Cloud Shell](https://shell.azure.com) den folgenden Befehl aus, um alle unterstützten Java-Versionen anzuzeigen:

```azurecli-interactive
az webapp list-runtimes --linux | grep "JAVA\|TOMCAT\|JBOSSEAP"
```

::: zone-end

## <a name="deploying-your-app"></a>Bereitstellen Ihrer App

### <a name="build-tools"></a>Build-Tools

#### <a name="maven"></a>Maven
Mit dem[Maven Plugin für Azure Web Apps](https://github.com/microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)können Sie Ihr Maven-Java-Projekt auf einfache Weise für Azure-Web-Apps mit einem Befehl im Projektstamm vorbereiten:

```shell
mvn com.microsoft.azure:azure-webapp-maven-plugin:2.2.0:config
```

Dieser Befehl fügt ein `azure-webapp-maven-plugin`-Plug-In und eine zugehörige Konfiguration hinzu, indem Sie aufgefordert werden, eine vorhandene Azure-Web-App auszuwählen oder eine neue zu erstellen. Dann können Sie Ihre Java-App mit dem folgenden Befehl in Azure bereitstellen:
```shell
mvn package azure-webapp:deploy
```

Nachfolgend eine Beispielkonfiguration in`pom.xml`:
```xml
<plugin> 
  <groupId>com.microsoft.azure</groupId>  
  <artifactId>azure-webapp-maven-plugin</artifactId>  
  <version>2.2.0</version>  
  <configuration>
    <subscriptionId>111111-11111-11111-1111111</subscriptionId>
    <resourceGroup>spring-boot-xxxxxxxxxx-rg</resourceGroup>
    <appName>spring-boot-xxxxxxxxxx</appName>
    <pricingTier>B2</pricingTier>
    <region>westus</region>
    <runtime>
      <os>Linux</os>      
      <webContainer>Java SE</webContainer>
      <javaVersion>Java 11</javaVersion>
    </runtime>
    <deployment>
      <resources>
        <resource>
          <type>jar</type>
          <directory>${project.basedir}/target</directory>
          <includes>
            <include>*.jar</include>
          </includes>
        </resource>
      </resources>
    </deployment>
  </configuration>
</plugin> 
```

#### <a name="gradle"></a>Gradle
1. Richten Sie das [Gradle-Plug-In für Azure-Web-Apps](https://github.com/microsoft/azure-gradle-plugins/tree/master/azure-webapp-gradle-plugin)ein, indem Sie das Plug-In ihrem `build.gradle` hinzufügen:
    ```groovy
    plugins {
      id "com.microsoft.azure.azurewebapp" version "1.2.0"
    }
    ```

1. Konfigurieren Sie Ihre Web-App-Details. Die entsprechenden Azure-Ressourcen werden erstellt, wenn sie nicht vorhanden sind.
Hier ist eine Beispielkonfiguration, Details finden Sie in diesem [Dokument](https://github.com/microsoft/azure-gradle-plugins/wiki/Webapp-Configuration).
    ```groovy
    azurewebapp {
        subscription = '<your subscription id>'
        resourceGroup = '<your resource group>'
        appName = '<your app name>'
        pricingTier = '<price tier like 'P1v2'>'
        region = '<region like 'westus'>'
        runtime {
          os = 'Linux'
          webContainer = 'Tomcat 9.0' // or 'Java SE' if you want to run an executable jar
          javaVersion = 'Java 8'
        }
        appSettings {
            <key> = <value>
        }
        auth {
            type = 'azure_cli' // support azure_cli, oauth2, device_code and service_principal
        }
    }
    ```

1. Installieren mit einem Befehl.
    ```shell
    gradle azureWebAppDeploy
    ```
    
### <a name="ides"></a>IDEs
Azure bietet nahtlose Java App Service-Entwicklungserfahrung in gängigen Java-IDEs, einschließlich:
- *VS Code*: [Java Web Apps mit Visual Studio Code](https://code.visualstudio.com/docs/java/java-webapp#_deploy-web-apps-to-the-cloud)
- *IntelliJ IDEA*:[Erstellen einer „Hello World“-Web-App für Azure App Service mithilfe von IntelliJ](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app)
- *Eclipse*:[Erstellen einer „Hello World“-Web-App für Azure App Service mithilfe von Eclipse](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)

### <a name="kudu-api"></a>Kudu-API
#### <a name="java-se"></a>Java SE

Um JAR-Dateien in Java SE bereitzustellen, verwenden Sie den `/api/publish/`-Endpunkt der Kudu-Website. Weitere Informationen zu dieser API finden Sie in [dieser Dokumentation](./deploy-zip.md#deploy-warjarear-packages). 

> [!NOTE]
>  Ihre JAR-Anwendung muss mit `app.jar` benannt werden, damit App Service die Anwendung identifizieren und ausführen kann. Das oben erwähnte Maven-Plug-In benennt Ihre Anwendung während der Bereitstellung automatisch für Sie um. Wenn Sie Ihre JAR-Datei nicht in *app.jar* umbenennen möchten, können Sie ein Shellskript mit dem Befehl zum Ausführen Ihrer JAR-App hochladen. Fügen Sie den absoluten Pfad zu diesem Skript im Abschnitt „Konfiguration“ des Portals in das Textfeld [Startdatei](./faq-app-service-linux.yml) ein. Das Startskript wird nicht aus dem Verzeichnis ausgeführt, in dem es abgelegt wurde. Verwenden Sie daher immer absolute Pfade, um auf Dateien in Ihrem Startskript zu verweisen (z. B.: `java -jar /home/myapp/myapp.jar`).

#### <a name="tomcat"></a>Tomcat

Um WAR-Dateien in Tomcat bereitzustellen, verwenden Sie den `/api/wardeploy/`-Endpunkt, um Ihre Archivdatei mit POST zu veröffentlichen. Weitere Informationen zu dieser API finden Sie in [dieser Dokumentation](./deploy-zip.md#deploy-warjarear-packages).

::: zone pivot="platform-linux"

#### <a name="jboss-eap"></a>JBoss EAP

Um WAR-Dateien in JBoss bereitzustellen, verwenden Sie den `/api/wardeploy/`-Endpunkt, um Ihre Archivdatei mit POST zu veröffentlichen. Weitere Informationen zu dieser API finden Sie in [dieser Dokumentation](./deploy-zip.md#deploy-warjarear-packages).

Zum Bereitstellen von EAR-Dateien [verwenden Sie FTP](deploy-ftp.md). Ihre EAR-Anwendung wird im Kontextstamm bereitgestellt, der in der Konfiguration Ihrer Anwendung definiert ist. Wenn der Kontextstamm Ihrer App beispielsweise `<context-root>myapp</context-root>` ist, können Sie die Site unter diesem `/myapp`-Pfad durchsuchen: `http://my-app-name.azurewebsites.net/myapp`. Wenn Sie möchten, dass Ihre Web-App im Stammpfad bedient wird, stellen Sie sicher, dass Ihre App den Kontextstamm auf den Stammpfad festlegt: `<context-root>/</context-root>`. Weitere Informationen finden Sie unter [Festlegen des Kontextstamms einer Webanwendung](https://docs.jboss.org/jbossas/guides/webguide/r2/en/html/ch06.html).

::: zone-end

Stellen Sie Ihre WAR- oder JAR-Dateien nicht mithilfe von FTP bereit. Der FTP-Tool dient zum Hochladen von Startskripts, Abhängigkeiten oder anderen Laufzeitdateien. Es ist nicht die optimale Wahl für die Bereitstellung von Web-Apps.

## <a name="logging-and-debugging-apps"></a>Protokollieren und Debuggen von Apps

Leistungsberichte, Datenverkehrsvisualisierungen und Integritätsprüfungen für die einzelnen Apps stehen über das Azure-Portal zur Verfügung. Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Diagnoseübersicht](overview-diagnostics.md).

### <a name="stream-diagnostic-logs"></a>Streamen von Diagnoseprotokollen

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

Weitere Informationen finden Sie unter [Streamen von Protokollen in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

::: zone pivot="platform-linux"

### <a name="ssh-console-access"></a>SSH-Konsolenzugriff

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="troubleshooting-tools"></a>Problembehandlungstools

Die integrierten Java-Images basieren auf dem [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)-Betriebssystem. Verwenden Sie den `apk`-Paket-Manager zur Installation von Tools oder Befehlen zur Problembehandlung.

::: zone-end

### <a name="flight-recorder"></a>Flight Recorder

Alle Java-Laufzeiten in App Service, die Azul-JVMs verwenden, enthalten Zulu Flight Recorder. Sie können damit JVM-, System- und Anwendungsereignisse aufzeichnen und Probleme in Ihren Java-Anwendungen beheben.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>Zeitgesteuerte Aufzeichnung

Für eine zeitgesteuerte Aufnahme benötigen Sie die PID (Prozess-ID) der Java-Anwendung. Öffnen Sie zum Ermitteln der PID einen Browser mit der SCM-Website Ihrer Web-App (`https://<your-site-name>.scm.azurewebsites.net/ProcessExplorer/`). Auf dieser Seite werden die in Ihrer Web-App ausgeführten Prozesse angezeigt. Suchen Sie in der Tabelle den Prozess mit dem Namen „Java“, und kopieren Sie die entsprechende PID (Prozess-ID).

Öffnen Sie dann auf der oberen Symbolleiste der SCM-Website die **Debugging-Konsole**, und führen Sie den folgenden Befehl aus. Ersetzen Sie `<pid>` durch die Prozess-ID, die Sie zuvor kopiert haben. Dieser Befehl startet eine 30-sekündige Profiler-Aufnahme Ihrer Java-Anwendung und erzeugt eine Datei mit dem Namen `timed_recording_example.jfr` im Verzeichnis `D:\home`.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

Stellen Sie eine SSH-Verbindung mit Ihrem App Service her, und führen Sie den `jcmd`-Befehl aus, um eine Liste aller laufenden Java-Prozesse zu sehen. Neben jcmd sollten Sie auch Ihre ausgeführte Java-Anwendung mit einer Prozess-ID (pid) sehen.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Führen Sie den folgenden Befehl aus, um eine 30-sekündige Aufnahme der JVM zu beginnen. Damit wird die JVM profiliert und eine JFR-Datei namens *jfr_example.jfr* im Stammverzeichnis erstellt. (Ersetzen Sie 116 mit der pid Ihrer Java-App.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Während des 30-Sekunden-Intervalls können Sie überprüfen, ob die Aufnahme stattfindet, indem Sie `jcmd 116 JFR.check` ausführen. Daraufhin werden alle Aufzeichnungen für den entsprechenden Java-Prozess gezeigt.

#### <a name="continuous-recording"></a>Fortlaufende Aufzeichnung

Sie können Zulu Flight Recorder einsetzen, um ein Profil Ihrer Java-Anwendung mit minimalen Auswirkungen auf die Laufzeitleistung zu erstellen. Führen Sie dafür den folgenden Azure CLI-Befehl aus, um eine App-Einstellung namens JAVA_OPTS mit der notwendigen Konfiguration zu erschaffen. Der Inhalt der JAVA_OPTS-App-Einstellung wird an den `java`-Befehl übermittelt, wenn Ihre App gestartet wird.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Nachdem die Aufzeichnung gestartet wurde, können Sie die aktuellen Aufzeichnungsdaten jederzeit mithilfe des `JFR.dump`-Befehls sichern.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>Analysieren von `.jfr`-Dateien

Verwenden Sie [FTPS](deploy-ftp.md) zum Herunterladen Ihrer JFR-Datei auf Ihren lokalen Computer. Laden Sie zum analysieren der JFR-Datei [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/) herunter und installieren Sie es. Weitere Informationen zu Zulu Mission Control finden Sie in [„Azul-Dokumentation“](https://docs.azul.com/zmc/) und den [Installationsanweisungen](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="app-logging"></a>App-Protokollierung

::: zone pivot="platform-windows"

Aktivieren Sie die [Anwendungsprotokollierung](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) über das Azure-Portal oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/webapp/log#az_webapp_log_config), um App Service so zu konfigurieren, dass die Streams mit der Standardkonsolenausgabe und den Standardkonsolenfehlern Ihrer Anwendung in das lokale Dateisystem oder Azure Blob Storage geschrieben werden. Die Protokollierung in der lokalen App Service-Dateisysteminstanz wird 12 Stunden nach der Konfiguration deaktiviert. Wenn Sie eine längere Beibehaltung benötigen, konfigurieren Sie die Anwendung für die Ausgabe in einen Blob Storage-Container. Ihre Java- und Tomcat-App-Protokolle befinden sich im Verzeichnis */home/LogFiles/Application/* .

::: zone-end
::: zone pivot="platform-linux"

Aktivieren Sie die [Anwendungsprotokollierung](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) über das Azure-Portal oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/webapp/log#az_webapp_log_config), um App Service so zu konfigurieren, dass die Streams mit der Standardkonsolenausgabe und den Standardkonsolenfehlern Ihrer Anwendung in das lokale Dateisystem oder Azure Blob Storage geschrieben werden. Wenn Sie eine längere Beibehaltung benötigen, konfigurieren Sie die Anwendung für die Ausgabe in einen Blob Storage-Container. Ihre Java- und Tomcat-App-Protokolle befinden sich im Verzeichnis */home/LogFiles/Application/* .

Die Protokollierung von Azure Blob Storage für Linux-basierte App Services kann nur mit [Azure Monitor](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor) konfiguriert werden. 

::: zone-end

Wenn Ihre Anwendung [Logback](https://logback.qos.ch/) oder [Log4j](https://logging.apache.org/log4j) für die Ablaufverfolgung verwendet, können Sie diese Ablaufverfolgungen mithilfe der Konfigurationsanweisungen für das Protokollierungsframework unter [Untersuchen von Java-Ablaufverfolgungsprotokollen in Application Insights](../azure-monitor/app/java-2x-trace-logs.md) zur Überprüfung an Azure Application Insights weiterleiten.

## <a name="customization-and-tuning"></a>Anpassung und Optimierung

Azure App Service unterstützt eine sofort verfügbare Optimierung und Anpassung über das Azure-Portal und die CLI. Lesen Sie die folgenden Artikel zur nicht Java-spezifischen Web-App-Konfiguration:

- [Konfigurieren von App-Einstellungen](configure-common.md#configure-app-settings)
- [Einrichten einer benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain.md)
- [Konfigurieren von TLS/SSL-Bindungen](configure-ssl-bindings.md)
- [Hinzufügen eines CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurieren der Kudu-Website](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Festlegen von Java-Runtimeoptionen

Um reservierten Arbeitsspeicher oder andere JVM-Runtimeoptionen festzulegen, erstellen Sie mit den Optionen eine [App-Einstellung](configure-common.md#configure-app-settings) mit dem Namen `JAVA_OPTS`. App Service übergibt diese Einstellung beim Start als Umgebungsvariable an die Java-Runtime.

Erstellen Sie im Azure-Portal unter **Anwendungseinstellungen** für die Webanwendung eine neue Anwendungseinstellung mit dem Namen `JAVA_OPTS` für Java SE oder `CATALINA_OPTS` für Tomcat, die weitere Einstellungen wie `-Xms512m -Xmx1204m` enthält.

Um die App-Einstellung über das Maven-Plug-In zu konfigurieren, fügen Sie Einstellungs-/Werttags im Azure-Plug-In-Abschnitt hinzu. Im folgenden Beispiel werden bestimmte Mindest- und Höchstwerte für die Java-Heapgröße festgelegt:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

::: zone pivot="platform-windows"

> [!NOTE]
> Sie müssen keine web.config-Datei erstellen, wenn Sie Tomcat unter Windows App Service verwenden. 

::: zone-end

Entwickler, die eine einzige Anwendung mit einem Bereitstellungsslot in ihrem App Service-Plan ausführen, können die folgenden Optionen verwenden:

- B1- und S1-Instanzen: `-Xms1024m -Xmx1024m`
- B2- und S2-Instanzen: `-Xms3072m -Xmx3072m`
- B3- und S3-Instanzen: `-Xms6144m -Xmx6144m`
- P1v2-Instanzen: `-Xms3072m -Xmx3072m`
- P2v2-Instanzen: `-Xms6144m -Xmx6144m`
- P3v2-Instanzen: `-Xms12800m -Xmx12800m`
- P1v3-Instanzen: `-Xms6656m -Xmx6656m`
- P2v3-Instanzen: `-Xms14848m -Xmx14848m`
- P3v3-Instanzen: `-Xms30720m -Xmx30720m`
- I1 Instanzen: `-Xms3072m -Xmx3072m`
- I2 Instanzen: `-Xms6144m -Xmx6144m`
- I3 Instanzen: `-Xms12800m -Xmx12800m`
- I1v2-Instanzen: `-Xms6656m -Xmx6656m`
- I2v2-Instanzen: `-Xms14848m -Xmx14848m`
- I3v2-Instanzen: `-Xms30720m -Xmx30720m`

Überprüfen Sie beim Optimieren Ihrer Anwendungsheapeinstellungen Ihre App Service-Plandetails, und berücksichtigen Sie mehrere Anwendungs- und Bereitstellungsslotanforderungen, um die optimale Zuordnung von Arbeitsspeicher zu ermitteln.

### <a name="turn-on-web-sockets"></a>Aktivieren von Websockets

Aktivieren Sie die Unterstützung für Websockets im Azure-Portal in den **Anwendungseinstellungen** für die Anwendung. Sie müssen die Anwendung neu starten, damit die Einstellung in Kraft tritt.

Aktivieren Sie die Websocketunterstützung über die Azure CLI mithilfe des folgenden Befehls:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starten Sie Ihre Anwendung anschließend neu:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Festlegen der Standardzeichencodierung

Erstellen Sie im Azure-Portal unter **Anwendungseinstellungen** für die Web-App eine neue App-Einstellung namens `JAVA_OPTS` mit dem Wert `-Dfile.encoding=UTF-8`.

Alternativ können Sie die App-Einstellung mit dem App Service-Maven-Plug-In konfigurieren. Fügen Sie die name- und value-Tags der Einstellung in der Plug-In-Konfiguration hinzu:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Vorkompilieren von JSP-Dateien

Zur Verbesserung der Leistung von Tomcat-Anwendungen können Sie die JSP-Dateien kompilieren, bevor sie in App Service bereitgestellt werden. Sie können das von Apache Sling bereitgestellte [Maven-Plug-In](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) oder diese [Ant-Builddatei](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation) verwenden.

## <a name="secure-applications"></a>Sichere Anwendungen

Für in App Service ausgeführte Java-Anwendungen gelten dieselben [bewährten Sicherheitsmethoden](../security/fundamentals/paas-applications-using-app-services.md) wie für andere Anwendungen.

### <a name="authenticate-users-easy-auth"></a>Authentifizieren von Benutzern (einfache Authentifizierung)

Richten Sie die App-Authentifizierung im Azure-Portal über die Option **Authentifizierung und Autorisierung** ein. Von dort aus können Sie die Authentifizierung über Azure Active Directory oder soziale Netzwerke wie Facebook, Google oder GitHub aktivieren. Die Konfiguration des Azure-Portals funktioniert nur, wenn Sie einen einzelnen Authentifizierungsanbieter konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren Ihrer App Service-App zur Verwendung der Azure Active Directory-Anmeldung](configure-authentication-provider-aad.md) und in den entsprechenden Artikeln für andere Identitätsanbieter. Wenn Sie mehrere Anmeldungsanbieter aktivieren müssen, sollten Sie die Anleitung im Artikel zum [Anpassen der An- und Abmeldungen](configure-authentication-customize-sign-in-out.md) befolgen.

#### <a name="java-se"></a>Java SE

Spring Boot-Entwickler können die [Azure Active Directory-Startoption für Spring Boot](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory) verwenden, um Anwendungen mithilfe von vertrauten Spring Security-Anmerkungen und -APIs absichern. Achten Sie darauf, dass Sie die maximale Headergröße in Ihrer *application.properties*-Datei erhöhen. Wir empfehlen den Wert `16384`.

#### <a name="tomcat"></a>Tomcat

Ihre Tomcat-Anwendung kann direkt aus dem Servlet auf die Ansprüche des Benutzers zugreifen, indem sie das Principal-Objekt in ein Map-Objekt umwandelt. Das Map-Objekt ordnet jeden Anspruchstyp einer Sammlung der Ansprüche für diesen Typ zu. Im folgenden Code ist `request` eine Instanz von `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nun können Sie das `Map`-Objekt auf bestimmte Ansprüche überprüfen. Der folgende Codeausschnitt durchläuft beispielsweise alle Anspruchstypen und gibt den Inhalt der einzelnen Sammlungen aus.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Um Benutzer abzumelden, verwenden Sie den Pfad `/.auth/ext/logout`. Weitere Aktionen finden Sie in der Dokumentation zu [Anpassen der An- und Abmeldungen](configure-authentication-customize-sign-in-out.md). Es gibt auch offizielle Dokumentation zur Tomcat [HttpServletRequest-Schnittstelle](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) und ihren Methoden. Die folgenden Servletmethoden werden auch basierend auf Ihrer App Service-Konfiguration aktualisiert:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Um dieses Feature zu deaktivieren, erstellen Sie eine Anwendungseinstellung mit dem Namen `WEBSITE_AUTH_SKIP_PRINCIPAL` und dem Wert `1`. Um alle Servletfilter zu deaktivieren, die von App Service hinzugefügt wurden, erstellen Sie eine Einstellung namens `WEBSITE_SKIP_FILTERS` mit dem Wert `1`.

### <a name="configure-tlsssl"></a>Konfigurieren von TLS/SSL

Befolgen Sie die Anleitung unter [Schützen eines benutzerdefinierten DNS-Namens mit einer TLS/SSL-Bindung in Azure App Service](configure-ssl-bindings.md), um ein vorhandenes TLS/SSL-Zertifikat hochzuladen und an den Domänennamen Ihrer Anwendung zu binden. Standardmäßig lässt Ihre Anwendung dennoch HTTP-Verbindungen zu. Befolgen Sie die spezifischen Schritte im Tutorial, um TLS/SSL zu erzwingen.

### <a name="use-keyvault-references"></a>Verwenden von KeyVault-Verweisen

[Azure KeyVault](../key-vault/general/overview.md) bietet eine zentrale Verwaltung von Geheimnissen mit Zugriffsrichtlinien und Überprüfungsverlauf. Sie können in KeyVault Geheimnisse (wie Kennwörter oder Verbindungszeichenfolgen) speichern und über Umgebungsvariablen auf diese Geheimnisse in Ihrer Anwendung zugreifen.

Befolgen Sie zunächst die Anweisungen zum [Gewähren des Zugriffs auf KeyVault für Ihre App](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) und zum [Erstellen eines KeyVault-Verweises auf Ihr Geheimnis in einer Anwendungseinstellung](app-service-key-vault-references.md#reference-syntax). Sie können überprüfen, ob der Verweis auf das Geheimnis aufgelöst wird, indem Sie die Umgebungsvariable ausgeben, während Sie remote auf das App Service-Terminal zugreifen.

Um diese Geheimnisse in Ihre Spring- oder Tomcat-Konfigurationsdatei einzufügen, verwenden Sie die Syntax der Umgebungsvariablen (`${MY_ENV_VAR}`). Für Spring-Konfigurationsdateien, siehe diese Dokumentation über [externalisierte Konfigurationen](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Verwenden des Java-Keystores

Standardmäßig werden alle öffentlichen oder privaten Zertifikate, die [in App Service Linux hochgeladen](configure-ssl-certificate.md) wurden, beim Start des Containers in den jeweiligen Java-Keystore geladen. Nach dem Hochladen Ihres Zertifikats müssen Sie Ihre App Service-Instanz neu starten, damit es in den Java-Keystore geladen wird. Öffentliche Zertifikate werden unter `$JAVA_HOME/jre/lib/security/cacerts` in den Schlüsselspeicher geladen, und private Zertifikate werden unter `$JAVA_HOME/lib/security/client.jks` gespeichert.

Für die Verschlüsselung Ihrer JDBC-Verbindung mit Zertifikaten im Java Key Store kann eine weitere Konfiguration erforderlich sein. Lesen Sie die Dokumentation für den von Ihnen gewählten JDBC-Treiber.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Initialisieren des Java-Schlüsselspeichers

Zum Initialisieren des `import java.security.KeyStore`-Objekts laden Sie die Schlüsselspeicherdatei mit dem Kennwort. Das Standardkennwort für beide Schlüsselspeicher lautet `changeit`.

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Manuelles Laden des Schlüsselspeichers

Sie können Zertifikate manuell in den Schlüsselspeicher laden. Erstellen Sie die App-Einstellung `SKIP_JAVA_KEYSTORE_LOAD` mit dem Wert `1`, damit die Zertifikate nicht automatisch von App Service in den Keystore geladen werden. Alle öffentlichen Zertifikate, die über das Azure-Portal in App Service hochgeladen werden, werden unter `/var/ssl/certs/` gespeichert. Private Zertifikate werden unter `/var/ssl/private/` gespeichert.

Sie können mit dem Java-Schlüsseltool interagieren oder Debuggingschritte ausführen, indem Sie eine [SSH-Verbindung](configure-linux-open-ssh-session.md) mit Ihrer App Service-Instanz herstellen und den Befehl `keytool`ausführen. Eine Liste mit Befehlen finden Sie in der [Dokumentation des Schlüsseltools](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html). Weitere Informationen über die KeyStore API finden Sie in [der offiziellen Dokumentation](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

::: zone-end

## <a name="configure-apm-platforms"></a>Konfigurieren von APM-Plattformen

In diesem Abschnitt wird veranschaulicht, wie Sie Java-Anwendungen, die in Azure App Service mit Azure Monitor Application Insights bereitgestellt werden, mit den Plattformen NewRelic und AppDynamics für die Überwachung der Anwendungsleistung (Application Performance Monitoring, APM) verbinden.

### <a name="configure-application-insights"></a>Application Insights konfigurieren

Azure Monitor Application Insights ist ein Cloud-nativer Anwendungsüberwachungsdienst, der es Kunden ermöglicht, Ausfälle, Engpässe und Nutzungsmuster zu beobachten, um die Anwendungsleistung zu verbessern und die mittlere Zeit bis zur Lösung (MTTR) zu reduzieren. Mit wenigen Klicks oder CLI-Befehlen können Sie die Überwachung für Ihre Node.js- oder Java-Apps aktivieren, wobei Protokolle, Metriken und verteilte Ablaufverfolgungen automatisch erfasst werden, sodass kein SDK in Ihre App eingeschlossen werden muss.

#### <a name="azure-portal"></a>Azure-Portal

Um Application Insights über das Azure-Portal zu aktivieren, wechseln Sie im Menü auf der linken Seite zu **Application Insights** und wählen **Application Insights aktivieren** aus. Standardmäßig wird eine neue Application Insights-Ressource mit demselben Namen wie Ihre Web-App verwendet. Sie können eine vorhandene Application Insights-Ressource verwenden oder den Namen ändern. Klicken Sie unten auf **Anwenden**.

#### <a name="azure-cli"></a>Azure CLI

Zum Aktivieren über die Azure CLI müssen Sie eine Application Insights-Ressource erstellen und einige App-Einstellungen im Azure-Portal festlegen, um Application Insights mit Ihrer Web-App zu verbinden.

1. Aktivieren der Applications Insights-Erweiterung

    ```bash
    az extension add -n application-insights
    ```

2. Erstellen Sie eine Application Insights-Ressource mit dem unten gezeigten CLI-Befehl. Ersetzen Sie die Platzhalter durch Ihren gewünschten Ressourcennamen und die Gruppe.

    ```bash
    az monitor app-insights component create --app <resource-name> -g <resource-group> --location westus2  --kind web --application-type web
    ```

    Notieren Sie sich die Werte für `connectionString` und `instrumentationKey`. Sie benötigen diese Werte im nächsten Schritt.

    > Um eine Liste anderer Standorte abzurufen, führen Sie `az account list-locations` aus.

::: zone pivot="platform-windows"
    
3. Legen Sie den Instrumentierungsschlüssel, die Verbindungszeichenfolge und die Version des Überwachungs-Agents als App-Einstellungen für die Web-App fest. Ersetzen Sie `<instrumentationKey>` und `<connectionString>` durch die Werte aus dem vorherigen Schritt.

    ```bash
    az webapp config appsettings set -n <webapp-name> -g <resource-group> --settings "APPINSIGHTS_INSTRUMENTATIONKEY=<instrumentationKey>" "APPLICATIONINSIGHTS_CONNECTION_STRING=<connectionString>" "ApplicationInsightsAgent_EXTENSION_VERSION=~3" "XDT_MicrosoftApplicationInsights_Mode=default" "XDT_MicrosoftApplicationInsights_Java=1"
    ```

::: zone-end
::: zone pivot="platform-linux"
    
3. Legen Sie den Instrumentierungsschlüssel, die Verbindungszeichenfolge und die Version des Überwachungs-Agents als App-Einstellungen für die Web-App fest. Ersetzen Sie `<instrumentationKey>` und `<connectionString>` durch die Werte aus dem vorherigen Schritt.

    ```bash
    az webapp config appsettings set -n <webapp-name> -g <resource-group> --settings "APPINSIGHTS_INSTRUMENTATIONKEY=<instrumentationKey>" "APPLICATIONINSIGHTS_CONNECTION_STRING=<connectionString>" "ApplicationInsightsAgent_EXTENSION_VERSION=~3" "XDT_MicrosoftApplicationInsights_Mode=default"
    ```

::: zone-end

### <a name="configure-new-relic"></a>Konfigurieren von NewRelic

::: zone pivot="platform-windows"

1. Erstellen Sie ein NewRelic-Konto unter [NewRelic.com](https://newrelic.com/signup).
2. Laden Sie den Java-Agent von NewRelic herunter. Der Dateiname hat etwa das Format *newrelic-java-x.x.x.zip*.
3. Kopieren Sie Ihren Lizenzschlüssel. Sie benötigen ihn später zum Konfigurieren des Agents.
4. [Stellen Sie eine SSH-Verbindung mit Ihrer App Service-Instanz her](configure-linux-open-ssh-session.md), und erstellen Sie das neue Verzeichnis */home/site/wwwroot/apm*.
5. Laden Sie die entpackten Dateien für den NewRelic-Java-Agent in ein Verzeichnis unter */home/site/wwwroot/apm* herunter. Die Dateien für Ihren Agenten sollten Sie unter */home/site/wwwroot/apm/newrelic* finden.
6. Ändern Sie die YAML-Datei unter */home/site/wwwroot/apm/newrelic/newrelic.yml*, und ersetzen Sie den Platzhalter-Lizenzwert durch Ihren eigenen Lizenzschlüssel.
7. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.

    - Erstellen Sie für **Java SE**-Apps eine Umgebungsvariable namens `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Erstellen Sie für **Tomcat**-Apps eine Umgebungsvariable namens `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

::: zone-end
::: zone pivot="platform-linux"

1. Erstellen Sie ein NewRelic-Konto unter [NewRelic.com](https://newrelic.com/signup).
2. Laden Sie den Java-Agent von NewRelic herunter. Der Dateiname hat etwa das Format *newrelic-java-x.x.x.zip*.
3. Kopieren Sie Ihren Lizenzschlüssel. Sie benötigen ihn später zum Konfigurieren des Agents.
4. [Stellen Sie eine SSH-Verbindung mit Ihrer App Service-Instanz her](configure-linux-open-ssh-session.md), und erstellen Sie das neue Verzeichnis */home/site/wwwroot/apm*.
5. Laden Sie die entpackten Dateien für den NewRelic-Java-Agent in ein Verzeichnis unter */home/site/wwwroot/apm* herunter. Die Dateien für Ihren Agenten sollten Sie unter */home/site/wwwroot/apm/newrelic* finden.
6. Ändern Sie die YAML-Datei unter */home/site/wwwroot/apm/newrelic/newrelic.yml*, und ersetzen Sie den Platzhalter-Lizenzwert durch Ihren eigenen Lizenzschlüssel.
7. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.
   
    - Erstellen Sie für **Java SE**-Apps eine Umgebungsvariable namens `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Erstellen Sie für **Tomcat**-Apps eine Umgebungsvariable namens `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

::: zone-end

>  Falls Sie bereits über eine Umgebungsvariable für `JAVA_OPTS` oder `CATALINA_OPTS` verfügen, können Sie die Option `-javaagent:/...` am Ende des aktuellen Werts anhängen.

### <a name="configure-appdynamics"></a>Konfigurieren von AppDynamics

::: zone pivot="platform-windows"

1. Erstellen Sie unter [AppDynamics.com](https://www.appdynamics.com/community/register/) ein AppDynamics-Konto.
2. Laden Sie den Java-Agent von der AppDynamics-Website herunter. Der Dateiname hat etwa das Format *AppServerAgent-x.x.x.xxxxx.zip*.
3. Verwenden Sie die [Kudu-Konsole](https://github.com/projectkudu/kudu/wiki/Kudu-console), um ein neues Verzeichnis namens */home/site/wwwroot/apm* zu erstellen.
4. Laden Sie die Dateien für den Java-Agent in ein Verzeichnis unter */home/site/wwwroot/apm* herunter. Die Dateien für Ihren Agenten sollten Sie unter */home/site/wwwroot/apm/appdynamics* finden.
5. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.

   - Erstellen Sie für **Java SE**-Apps eine Umgebungsvariable namens `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.
   - Erstellen Sie für **Tomcat**-Apps eine Umgebungsvariable namens `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.

::: zone-end
::: zone pivot="platform-linux"

1. Erstellen Sie unter [AppDynamics.com](https://www.appdynamics.com/community/register/) ein AppDynamics-Konto.
2. Laden Sie den Java-Agent von der AppDynamics-Website herunter. Der Dateiname hat etwa das Format *AppServerAgent-x.x.x.xxxxx.zip*.
3. [Stellen Sie eine SSH-Verbindung mit Ihrer App Service-Instanz her](configure-linux-open-ssh-session.md), und erstellen Sie das neue Verzeichnis */home/site/wwwroot/apm*.
4. Laden Sie die Dateien für den Java-Agent in ein Verzeichnis unter */home/site/wwwroot/apm* herunter. Die Dateien für Ihren Agenten sollten Sie unter */home/site/wwwroot/apm/appdynamics* finden.
5. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.

   - Erstellen Sie für **Java SE**-Apps eine Umgebungsvariable namens `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.
   - Erstellen Sie für **Tomcat**-Apps eine Umgebungsvariable namens `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.

::: zone-end

> [!NOTE]
>  Falls Sie bereits über eine Umgebungsvariable für `JAVA_OPTS` oder `CATALINA_OPTS` verfügen, können Sie die Option `-javaagent:/...` am Ende des aktuellen Werts anhängen.

## <a name="configure-data-sources"></a>Konfigurieren von Datenquellen

### <a name="java-se"></a>Java SE

Für das Verbinden mit Datenquellen in Spring Boot-Anwendungen wird empfohlen, Verbindungszeichenfolgen zu erstellen und in Ihre Datei *application.properties* einzufügen.

1. Legen Sie auf der App Service-Seite im Abschnitt „Konfiguration“ einen Namen für die Zeichenfolge fest, fügen Sie Ihre JDBC-Verbindungszeichenfolge in das Feld für den Wert ein, und legen Sie den Typ auf „Benutzerdefiniert“ fest. Sie können diese Verbindungszeichenfolge optional als Sloteinstellung festlegen.

    Diese Verbindungszeichenfolge ist für die Anwendung als die Umgebungsvariable `CUSTOMCONNSTR_<your-string-name>` verfügbar. Die Verbindungszeichenfolge, die Sie oben erstellt haben, erhält z. B. den Namen `CUSTOMCONNSTR_exampledb`.

2. Verweisen Sie in Ihrer Datei *application.properties* über den Namen der Umgebungsvariable auf diese Verbindungszeichenfolge. In unserem Beispiel würden wir Folgendes verwenden.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Weitere Informationen zu diesem Thema finden Sie in der [Spring Boot Dokumentation über Datenzugriff](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) und [externalisierte Konfigurationen](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

Diese Anweisungen gelten für alle Datenbankverbindungen. Für die Platzhalter müssen der Treiberklassenname und die JAR-Datei der gewählten Datenbank angegeben werden. In der folgenden Tabelle finden Sie Klassennamen und Treiberdownloads für gängige Datenbanken:

| Datenbank   | Treiberklassenname                             | JDBC-Treiber                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Download](https://dev.mysql.com/downloads/connector/j/) (Wählen Sie „Platform Independent“ (Plattformunabhängig) aus.) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)                                                           |

Wenn Sie Tomcat für die Verwendung von Java Database Connectivity (JDBC) oder der Java-Persistenz-API (JPA) konfigurieren möchten, passen Sie zuerst die Umgebungsvariable `CATALINA_OPTS` an, die von Tomcat beim Start eingelesen wird. Diese Werte können mithilfe einer App-Einstellung im [App Service-Maven-Plug-In](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) festgelegt werden:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Alternativ können Sie die Umgebungsvariablen im Azure-Portal auf der Seite **Konfiguration** > **Anwendungseinstellungen** festlegen.

Legen Sie als Nächstes fest, ob die Datenquelle nur für eine einzelne Anwendung oder für alle im Tomcat-Servlet ausgeführten Anwendungen verfügbar sein soll.

#### <a name="application-level-data-sources"></a>Datenquellen auf Anwendungsebene

1. Erstellen Sie eine *context.xml*-Datei im Verzeichnis *META-INF/* Ihres Projekts. Erstellen Sie das Verzeichnis *META-INF/* , falls es noch nicht vorhanden ist.

2. Fügen Sie in *context.xml* ein Element vom Typ `Context` hinzu, um die Datenquelle mit einer JNDI-Adresse zu verknüpfen. Ersetzen Sie den Platzhalter `driverClassName` durch den Klassennamen Ihres Treibers aus der obigen Tabelle.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Aktualisieren Sie die Datei *web.xml* Ihrer Anwendung, sodass die Datenquelle in Ihrer Anwendung verwendet wird.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Gemeinsam verwendete Ressourcen auf Serverebene

Tomcat-Installationen auf App Service unter Windows sind im freigegebenen Bereich im App Service-Plan vorhanden. Sie können eine Tomcat-Installation nicht direkt für die serverweite Konfiguration ändern. Um Konfigurationsänderungen auf Serverebene an Ihrer Tomcat-Installation vorzunehmen, müssen Sie Tomcat in einen lokalen Ordner kopieren, in dem Sie die Konfiguration von Tomcat ändern können. 

##### <a name="automate-creating-custom-tomcat-on-app-start"></a>Automatisieren der Erstellung von benutzerdefiniertem Tomcat beim App-Start

Sie können ein Startskript verwenden, um Aktionen auszuführen, bevor eine Web-App gestartet wird. Das Startskript zum Anpassen von Tomcat muss die folgenden Schritte ausführen:

1. Überprüfen, ob Tomcat bereits kopiert und lokal konfiguriert wurde. Wenn dies der Fall ist, kann das Startskript hier enden.
2. Tomcat lokal kopieren.
3. Die erforderlichen Konfigurationsänderungen vornehmen.
4. Angeben, dass die Konfiguration erfolgreich abgeschlossen wurde.

Erstellen Sie für Windows-Sites im Verzeichnis `wwwroot` eine Datei mit dem Namen `startup.cmd` oder `startup.ps1`. Diese wird automatisch ausgeführt, bevor der Tomcat-Server gestartet wird.

Hier sehen Sie ein PowerShell-Skript, mit dem diese Schritte ausgeführt werden:

```powershell
    # Check for marker file indicating that config has already been done
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }

    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat"){
        Remove-Item "$Env:LOCAL_EXPANDED\tomcat" --recurse
    }

    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$Env:AZURE_TOMCAT90_HOME\*" -Destination "$Env:LOCAL_EXPANDED\tomcat" -Recurse

    # Perform the required customization of Tomcat
    {... customization ...}

    # Mark that the operation was a success
    New-Item -Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
```

##### <a name="transforms"></a>Transformationen

Ein häufiger Anwendungsfall zum Anpassen einer Tomcat-Version ist das Ändern der Konfigurationsdateien `server.xml`, `context.xml` oder `web.xml` für Tomcat. App Service ändert diese Dateien bereits, um Plattformfunktionen zur Verfügung zu stellen. Um diese Funktionen weiterhin zu verwenden, ist es wichtig, den Inhalt dieser Dateien zu erhalten, wenn Sie Änderungen daran vornehmen. Um dies zu erreichen, wird empfohlen, eine [XSL-Transformation (XSLT)](https://www.w3schools.com/xml/xsl_intro.asp) zu verwenden. Verwenden Sie eine XSL-Transformation, um Änderungen an den XML-Dateien vorzunehmen, während der ursprüngliche Inhalt der Datei beibehalten wird.

###### <a name="example-xslt-file"></a>XSLT-Beispieldatei

Diese Beispieltransformation fügt einen neuen Connector-Knoten zu `server.xml` hinzu. Beachten Sie die *Identitätstransformation*, die den ursprünglichen Inhalt der Datei beibehält.

```xml
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
    <xsl:output method="xml" indent="yes"/>
  
    <!-- Identity transform: this ensures that the original contents of the file are included in the new file -->
    <!-- Ensure that your transform files include this block -->
    <xsl:template match="@* | node()" name="Copy">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()"/>
      </xsl:copy>
    </xsl:template>
  
    <xsl:template match="@* | node()" mode="insertConnector">
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                   contains(., '&lt;Connector') and
                                   (contains(., 'scheme=&quot;https&quot;') or
                                    contains(., &quot;scheme='https'&quot;))]">
      <xsl:value-of select="." disable-output-escaping="yes" />
    </xsl:template>
  
    <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                     comment()[contains(., '&lt;Connector') and
                                               (contains(., 'scheme=&quot;https&quot;') or
                                                contains(., &quot;scheme='https'&quot;))]
                                    )]
                        ">
      <xsl:copy>
        <xsl:apply-templates select="@* | node()" mode="insertConnector" />
      </xsl:copy>
    </xsl:template>
  
    <!-- Add the new connector after the last existing Connnector if there is one -->
    <xsl:template match="Connector[last()]" mode="insertConnector">
      <xsl:call-template name="Copy" />
  
      <xsl:call-template name="AddConnector" />
    </xsl:template>
  
    <!-- ... or before the first Engine if there is no existing Connector -->
    <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                  mode="insertConnector">
      <xsl:call-template name="AddConnector" />
  
      <xsl:call-template name="Copy" />
    </xsl:template>
  
    <xsl:template name="AddConnector">
      <!-- Add new line -->
      <xsl:text>&#xa;</xsl:text>
      <!-- This is the new connector -->
      <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
                 maxThreads="150" scheme="https" secure="true" 
                 keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
                 clientAuth="false" sslProtocol="TLS" />
    </xsl:template>

    </xsl:stylesheet>
```

###### <a name="function-for-xsl-transform"></a>Funktion für XSL-Transformation

PowerShell verfügt über integrierte Tools zum Transformieren von XML-Dateien mithilfe von XSL-Transformationen. Das folgende Skript ist eine Beispielfunktion, die Sie in `startup.ps1` verwenden können, um die Transformation auszuführen:

```powershell
    function TransformXML{
        param ($xml, $xsl, $output)

        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }

        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;

            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);

        }

        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            Write-Host  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
```

##### <a name="app-settings"></a>App-Einstellungen

Der Plattform muss auch bekannt sein, wo Ihre benutzerdefinierte Version von Tomcat installiert ist. Sie können den Speicherort der Installation in der `CATALINA_BASE` App-Einstellung festlegen.

Sie können die Azure CLI (Azure-Befehlszeilenschnittstelle) nutzen, um diese Einstellung zu ändern:

```powershell
    az webapp config appsettings set -g $MyResourceGroup -n $MyUniqueApp --settings CATALINA_BASE="%LOCAL_EXPANDED%\tomcat"
```

Alternativ können Sie die Einstellung im Azure-Portal manuell ändern:

1. Gehen Sie zu **Einstellungen** > **Konfiguration** > **Anwendungseinstellungen**.
1. Wählen Sie **Neue Anwendungseinstellung** aus.
1. Verwenden Sie folgende Werte, um die Einstellung zu erstellen:
   1. **Name**: `CATALINA_BASE`
   1. **Wert**: `"%LOCAL_EXPANDED%\tomcat"`

##### <a name="example-startupps1"></a>Beispiel: „startup.ps1“

Das folgende Beispielskript kopiert einen benutzerdefinierten Tomcat in einen lokalen Ordner, führt eine XSL-Transformation aus und gibt an, dass die Transformation erfolgreich war:

```powershell
    # Locations of xml and xsl files
    $target_xml="$Env:LOCAL_EXPANDED\tomcat\conf\server.xml"
    $target_xsl="$Env:HOME\site\server.xsl"
    
    # Define the transform function
    # Useful if transforming multiple files
    function TransformXML{
        param ($xml, $xsl, $output)
    
        if (-not $xml -or -not $xsl -or -not $output)
        {
            return 0
        }
    
        Try
        {
            $xslt_settings = New-Object System.Xml.Xsl.XsltSettings;
            $XmlUrlResolver = New-Object System.Xml.XmlUrlResolver;
            $xslt_settings.EnableScript = 1;
    
            $xslt = New-Object System.Xml.Xsl.XslCompiledTransform;
            $xslt.Load($xsl,$xslt_settings,$XmlUrlResolver);
            $xslt.Transform($xml, $output);
        }
    
        Catch
        {
            $ErrorMessage = $_.Exception.Message
            $FailedItem = $_.Exception.ItemName
            echo  'Error'$ErrorMessage':'$FailedItem':' $_.Exception;
            return 0
        }
        return 1
    }
    
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml
    
    # Check for marker file indicating that config has already been done
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker"){
        return 0
    }
    
    # Delete previous Tomcat directory if it exists
    # In case previous config could not be completed or a new config should be forcefully installed
    if(Test-Path "$Env:LOCAL_EXPANDED\tomcat"){
        Remove-Item "$Env:LOCAL_EXPANDED\tomcat" --recurse
    }
    
    md -Path "$Env:LOCAL_EXPANDED\tomcat"
    
    # Copy Tomcat to local
    # Using the environment variable $AZURE_TOMCAT90_HOME uses the 'default' version of Tomcat
    Copy-Item -Path "$Env:AZURE_TOMCAT90_HOME\*" "$Env:LOCAL_EXPANDED\tomcat" -Recurse
    
    # Perform the required customization of Tomcat
    $success = TransformXML -xml $target_xml -xsl $target_xsl -output $target_xml
    
    # Mark that the operation was a success if successful
    if($success){
        New-Item -Path "$Env:LOCAL_EXPANDED\tomcat\config_done_marker" -ItemType File
    }
```

#### <a name="finalize-configuration"></a>Abschließen der Konfiguration

Platzieren Sie abschließend die JAR-Dateien des Treibers im Tomcat-Klassenpfad, und starten Sie Ihre App Service-Instanz neu. Stellen Sie sicher, dass die JDBC-Treiberdateien für den Tomcat-Klassenladeprogramm verfügbar sind, indem Sie sie im Verzeichnis */home/tomcat/lib* ablegen. (Erstellen Sie dieses Verzeichnis, falls es noch nicht vorhanden ist.) Um diese Dateien in Ihre App Service-Instanz hochzuladen, gehen Sie folgendermaßen vor:

1. Installieren Sie in der [Cloud Shell](https://shell.azure.com) die Web-App-Erweiterung:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Führen Sie den folgenden CLI-Befehl aus, um einen SSH-Tunnel von Ihrem lokalen System zu App Service zu erstellen:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Stellen Sie mit Ihrem SFTP-Client eine Verbindung mit dem lokalen Tunnelport her, und laden Sie die Dateien in den Ordner */home/tomcat/lib* hoch.

Alternativ können Sie zum Hochladen des JDBC-Treibers auch einen FTP-Client verwenden. Führen Sie dazu die [Schritte zum Abrufen Ihrer FTP-Anmeldeinformationen](deploy-configure-credentials.md) aus.

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

Diese Anweisungen gelten für alle Datenbankverbindungen. Für die Platzhalter müssen der Treiberklassenname und die JAR-Datei der gewählten Datenbank angegeben werden. In der folgenden Tabelle finden Sie Klassennamen und Treiberdownloads für gängige Datenbanken:

| Datenbank   | Treiberklassenname                             | JDBC-Treiber                                                                              |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Download](https://dev.mysql.com/downloads/connector/j/) (Wählen Sie „Platform Independent“ (Plattformunabhängig) aus.) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server#download)     |

Wenn Sie Tomcat für die Verwendung von Java Database Connectivity (JDBC) oder der Java-Persistenz-API (JPA) konfigurieren möchten, passen Sie zuerst die Umgebungsvariable `CATALINA_OPTS` an, die von Tomcat beim Start eingelesen wird. Diese Werte können mithilfe einer App-Einstellung im [App Service-Maven-Plug-In](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) festgelegt werden:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Alternativ können Sie die Umgebungsvariablen im Azure-Portal auf der Seite **Konfiguration** > **Anwendungseinstellungen** festlegen.

Legen Sie als Nächstes fest, ob die Datenquelle nur für eine einzelne Anwendung oder für alle im Tomcat-Servlet ausgeführten Anwendungen verfügbar sein soll.

#### <a name="application-level-data-sources"></a>Datenquellen auf Anwendungsebene

1. Erstellen Sie eine *context.xml*-Datei im Verzeichnis *META-INF/* Ihres Projekts. Erstellen Sie das Verzeichnis *META-INF/* , falls es noch nicht vorhanden ist.

2. Fügen Sie in *context.xml* ein Element vom Typ `Context` hinzu, um die Datenquelle mit einer JNDI-Adresse zu verknüpfen. Ersetzen Sie den Platzhalter `driverClassName` durch den Klassennamen Ihres Treibers aus der obigen Tabelle.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Aktualisieren Sie die Datei *web.xml* Ihrer Anwendung, sodass die Datenquelle in Ihrer Anwendung verwendet wird.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Gemeinsam verwendete Ressourcen auf Serverebene

Wenn Sie eine gemeinsam genutzte Datenquelle auf Serverebene hinzufügen, müssen Sie die Datei „server.xml“ von Tomcat bearbeiten. Laden Sie zunächst ein [Startskript](./faq-app-service-linux.yml) hoch, und legen Sie unter **Konfiguration** > **Startbefehl** den Pfad des Skripts fest. Das Startskript kann per [FTP](deploy-ftp.md) hochgeladen werden.

Ihr Startskript führt eine [XSL-Transformation](https://www.w3schools.com/xml/xsl_intro.asp) für die Datei „server.xml“ durch und gibt die resultierende XML-Datei unter `/usr/local/tomcat/conf/server.xml` aus. Das Startskript muss „libxslt“ per APK installieren. Die XSL-Datei und das Startskript können per FTP hochgeladen werden. Im Anschluss finden Sie ein Beispiel für ein Startskript:

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Im Anschluss folgt ein Beispiel für eine XSL-Datei. Die XSL-Beispieldatei fügt der Datei „server.xml“ von Tomcat einen neuen Connectorknoten hinzu.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Abschließen der Konfiguration

Platzieren Sie abschließend die JAR-Dateien des Treibers im Tomcat-Klassenpfad, und starten Sie Ihre App Service-Instanz neu.

1. Stellen Sie sicher, dass die JDBC-Treiberdateien für den Tomcat-Klassenladeprogramm verfügbar sind, indem Sie sie im Verzeichnis */home/tomcat/lib* ablegen. (Erstellen Sie dieses Verzeichnis, falls es noch nicht vorhanden ist.) Um diese Dateien in Ihre App Service-Instanz hochzuladen, gehen Sie folgendermaßen vor:

    1. Installieren Sie in der [Cloud Shell](https://shell.azure.com) die Web-App-Erweiterung:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Führen Sie den folgenden CLI-Befehl aus, um einen SSH-Tunnel von Ihrem lokalen System zu App Service zu erstellen:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Stellen Sie mit Ihrem SFTP-Client eine Verbindung mit dem lokalen Tunnelport her, und laden Sie die Dateien in den Ordner */home/tomcat/lib* hoch.

    Alternativ können Sie zum Hochladen des JDBC-Treibers auch einen FTP-Client verwenden. Führen Sie dazu die [Schritte zum Abrufen Ihrer FTP-Anmeldeinformationen](deploy-configure-credentials.md) aus.

2. Wenn Sie eine Datenquelle auf Serverebene erstellt haben, starten Sie die App Service-Linux-Anwendung neu. Tomcat setzt `CATALINA_BASE` auf `/home/tomcat` zurück und verwendet die aktualisierte Konfiguration.

### <a name="jboss-eap"></a>JBoss EAP

Das [Registrieren einer Datenquelle in JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management) umfasst drei Hauptschritte: Hochladen des JDBC-Treibers, Hinzufügen des JDBC-Treibers als Modul und Registrieren des Moduls. App Service ist ein zustandsloser Hostingdienst, weshalb die Konfigurationsbefehle zum Hinzufügen und Registrieren des Datenquellenmoduls als Skript geschrieben und beim Start des Containers angewendet werden müssen.

1. Rufen Sie den JDBC-Treiber Ihrer Datenbank ab. 
2. Erstellen Sie eine XML-Moduldefinitionsdatei für den JDBC-Treiber. Das folgende Beispiel zeigt eine Moduldefinition für PostgreSQL.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. Fügen Sie Ihre JBoss-CLI-Befehle in eine Datei namens `jboss-cli-commands.cli` ein. Die JBoss-Befehle müssen das Modul hinzufügen und es als Datenquelle registrieren. Das folgende Beispiel zeigt die JBoss-CLI-Befehle für PostgreSQL.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. Erstellen Sie ein Startskript namens `startup_script.sh`, das die JBoss-CLI-Befehle aufruft. Im unten stehenden Beispiel wird veranschaulicht, wie Sie Ihr `jboss-cli-commands.cli` aufrufen. Später konfigurieren Sie App Service dann für die Ausführung dieses Skripts, wenn der Container startet. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. Wenn Sie einen FTP-Client Ihrer Wahl verwenden, laden Sie Ihren JDBC-Treiber, `jboss-cli-commands.cli`, `startup_script.sh` und die Moduldefinition in `/site/deployments/tools/` hoch.
2. Konfigurieren Sie Ihre Website so, dass sie `startup_script.sh` beim Start des Containers ausführt. Navigieren Sie im Azure-Portal zu **Konfiguration** > **Allgemeine Einstellungen** > **Startbefehl**. Legen Sie das Feld „Startbefehl“ auf `/home/site/deployments/tools/startup_script.sh` fest. **Speichern** Sie die Änderungen.

Um sich zu vergewissern, dass die Datenquelle dem JBoss-Server hinzugefügt wurde, stellen Sie eine SSH-Verbindung mit Ihrer Web-App her, und führen Sie `$JBOSS_HOME/bin/jboss-cli.sh --connect` aus. Sobald Sie mit JBoss verbunden sind, führen Sie `/subsystem=datasources:read-resource` aus, um eine Liste der Datenquellen auszugeben.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Auswählen einer Java Runtime-Version

App Service ermöglicht es den Benutzern, die Hauptversion der JVM, wie Java 8 oder Java 11, und die Nebenversion, wie 1.8.0_232 oder 11.0.5, auszuwählen. Sie können auch auswählen, dass die Nebenversion automatisch aktualisiert wird, sobald neue Nebenversionen verfügbar werden. In den meisten Fällen sollten Produktionsstandorte angeheftete JVM-Nebenversionen verwenden. Dadurch werden unerwartete Ausfälle während einer automatischen Aktualisierung einer Nebenversion verhindert. Alle Java-Web-Apps verwenden 64-Bit-JVMs, was nicht konfigurierbar ist.

Wenn Sie sich für das Anheften der Nebenversion entscheiden, müssen Sie die JVM-Nebenversion in regelmäßigen Abständen am Standort aktualisieren. Um sicherzustellen, dass Ihre Anwendung mit der neueren Nebenversion funktioniert, erstellen Sie einen Stagingslot, und erhöhen Sie die Nebenversion am Stagingstandort. Nachdem Sie bestätigt haben, dass die Anwendung in der neuen Nebenversion ordnungsgemäß ausgeführt wird, können Sie den Stagingbereich gegen den Produktionsslot austauschen.

::: zone pivot="platform-linux"

## <a name="jboss-eap-app-service-plans"></a>JBoss EAP App Service-Pläne
<a id="jboss-eap-hardware-options"></a>

JBoss EAP ist nur für die App Service-Plantypen „Premium v3“ und „Isoliert v2“ verfügbar. Kunden, die während der öffentlichen Vorschauphase eine JBoss EAP-Site in einem anderen Tarif erstellt haben, sollten auf die Hardwareebene „Premium“ oder „Isoliert“ hochskalieren, um unerwartetes Verhalten zu vermeiden.

::: zone-end

## <a name="java-runtime-statement-of-support"></a>Unterstützungserklärung für die Java-Runtime

### <a name="jdk-versions-and-maintenance"></a>JDK-Versionen und -Wartung

Als Java Development Kit (JDK) wird das von [Azul Systems](https://www.azul.com/) bereitgestellte [Zulu](https://www.azul.com/downloads/azure-only/zulu/) von Azure unterstützt. Bei Azul Zulu Enterprise-Builds von OpenJDK handelt es sich um eine kostenlose, plattformübergreifende und produktionsbereite Distribution von OpenJDK für Azure und Azure Stack, die von Microsoft und Azul Systems unterstützt wird. Sie enthält alle Komponenten, die zum Erstellen und Ausführen von Java SE-Anwendungen benötigt werden. Sie können die JDK aus der [Java JDK-Installation](/azure/developer/java/fundamentals/java-support-on-azure) heraus installieren.

Aktualisierungen von Hauptversionen werden in Azure App Service durch neue Laufzeitoptionen bereitgestellt. Kunden führen das Update auf diese neueren Versionen von Java durch die Konfiguration ihrer App Service-Bereitstellung durch und müssen durch Tests sicherstellen, dass das größere Update ihren Anforderungen entspricht.

Unterstützte JDKs werden jedes Vierteljahr im Januar, April, Juli und Oktober automatisch gepatcht. Weitere Informationen zu Java auf Azure finden Sie in [diesem Support-Dokument](/azure/developer/java/fundamentals/java-support-on-azure).

### <a name="security-updates"></a>Sicherheitsupdates

Patches und Fixes für größere Sicherheitsrisiken werden veröffentlicht, sobald sie von Azul Systems zur Verfügung gestellt werden. Ein „größeres“ Sicherheitsrisiko wird durch eine Gesamtbewertung von 9.0 oder höher im [NIST Common Vulnerability Scoring System, Version 2](https://nvd.nist.gov/vuln-metrics/cvss), definiert.

Tomcat 8.0 hat [am 30. September 2018 das Ende der Lebensdauer (End Of Life, EOL) erreicht](https://tomcat.apache.org/tomcat-80-eol.html). Die Runtime ist zwar noch in Azure App Service verfügbar, von Azure werden jedoch keine Sicherheitsupdates mehr auf Tomcat 8.0 angewendet. Migrieren Sie Ihre Anwendungen nach Möglichkeit zu Tomcat 8.5 oder 9.0. Sowohl Tomcat 8.5 als auch Tomcat 9.0 steht in Azure App Service zur Verfügung. Weitere Informationen finden Sie auf der [offiziellen Tomcat-Website](https://tomcat.apache.org/whichversion.html). 

### <a name="deprecation-and-retirement"></a>Einstellung und Außerbetriebnahme

Wenn eine unterstützte Java-Runtime eingestellt wird, erhalten Azure-Entwickler, die die betreffende Runtime verwenden, mindestens sechs Monate vor Außerbetriebnahme der Runtime eine entsprechende Benachrichtigung.


### <a name="local-development"></a>Lokale Entwicklung

Entwickler können die Production Edition des Azul Zulu Enterprise JDK zur lokalen Entwicklung von der [Azul-Downloadsite](https://www.azul.com/downloads/azure-only/zulu/) herunterladen.

### <a name="development-support"></a>Entwicklungsunterstützung

Produktsupport für das von [Azure unterstützte Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) ist bei der Entwicklung für Azure oder [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) mit einem [qualifizierten Azure-Supportplan](https://azure.microsoft.com/support/plans/) von Microsoft erhältlich.

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie das Center [Azure für Java-Entwickler](/java/azure/), um Azure-Schnellstarts, Tutorials und Java-Referenzdokumentation zu finden.

- [Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux](faq-app-service-linux.yml)
- [Referenz zu Umgebungsvariablen und App-Einstellungen](reference-app-settings.md)
