---
title: Dienst-zu-Dienst-Authentifizierung – Data Lake Storage Gen2 – Java SDK
description: Hier erfahren Sie, wie Sie mithilfe von Azure Active Directory und Java die Dienst-zu-Dienst-Authentifizierung in Azure Data Lake Storage Gen2 implementieren.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: normesta
ms.openlocfilehash: 8f36eaef8c84afd1010d6e4ab2714b0d538028b9
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494543"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen2-using-java"></a>Dienst-zu-Dienst-Authentifizierung in Azure Data Lake Storage Gen2 mithilfe von Java

> [!div class="op_single_selector"]
> * [Verwenden von Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Verwenden des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Verwenden von Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Verwenden der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

In diesem Artikel erfahren Sie, wie Sie mithilfe des Java SDK die Dienst-zu-Dienst-Authentifizierung in Azure Data Lake Storage Gen2 durchführen. Die Authentifizierung von Endbenutzer*innen in Data Lake Storage Gen2 mit dem Java SDK wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

* **Erstellen einer Azure Active Directory-Webanwendung.** Sie müssen die Schritte unter [Dienst-zu-Dienst-Authentifizierung bei Azure Data Lake Storage Gen2 mithilfe von Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md) ausgeführt haben.

* [Maven](https://maven.apache.org/install.html). Dieses Tutorial verwendet Maven für die Erstellung und für Projektabhängigkeiten. Obwohl die Erstellung auch ohne ein Buildsystem wie Maven oder Gradle möglich ist, lassen sich Abhängigkeiten mit einem solchen System viel einfacher verwalten.

* (Optional) Eine IDE wie [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) oder [Eclipse](https://www.eclipse.org/downloads/).

## <a name="service-to-service-authentication"></a>Dienst-zu-Dienst-Authentifizierung

1. Erstellen Sie ein Maven-Projekt. Verwenden Sie hierfür [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) in der Befehlszeile oder eine IDE. Eine Anleitung zum Erstellen eines Java-Projekts mit IntelliJ finden Sie [hier](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Eine Anleitung zum Erstellen eines Projekts mit Eclipse finden Sie [hier](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Fügen Sie Ihrer Maven-Datei **pom.xml** die folgenden Abhängigkeiten hinzu. Fügen Sie den folgenden Codeausschnitt vor dem Tag **\</project>** hinzu:

    ```xml
    <dependencies>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-storage-file-datalake</artifactId>
          <version>12.6.0</version>
      </dependency>
      <dependency>
          <groupId>com.azure</groupId>
          <artifactId>azure-identity</artifactId>
          <version>1.3.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    Die erste Abhängigkeit ist die Verwendung des Data Lake Storage Gen2 SDK (`azure-storage-file-datalake`) aus dem Maven-Repository. Die zweite Abhängigkeit ist die Angabe des Protokollierungsframeworks (`slf4j-nop`) für diese App. Das Data Lake Storage Gen2 SDK verwendet die Protokollierungsfassade [slf4j](https://www.slf4j.org/), bei der Sie zwischen mehreren gängigen Protokollierungsframeworks wie log4j, Java-Protokollierung und Logback auswählen oder die Protokollierung deaktivieren können. Da wir im vorliegenden Beispiel die Protokollierung deaktivieren möchten, verwenden wir die Bindung **slf4j-nop**. Informationen zur Verwendung anderer Protokollierungsoptionen in Ihrer App finden Sie unter [Deklarieren von Projektabhängigkeiten für die Protokollierung](https://www.slf4j.org/manual.html#projectDep).

3. Fügen Sie Ihrer Anwendung die folgenden Importanweisungen hinzu.

    ```java
    import com.azure.identity.ClientSecretCredential;
    import com.azure.identity.ClientSecretCredentialBuilder;
    import com.azure.storage.file.datalake.DataLakeDirectoryClient;
    import com.azure.storage.file.datalake.DataLakeFileClient;
    import com.azure.storage.file.datalake.DataLakeServiceClient;
    import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
    import com.azure.storage.file.datalake.DataLakeFileSystemClient;
    import com.azure.storage.file.datalake.models.ListPathsOptions;
    import com.azure.storage.file.datalake.models.PathAccessControl;
    import com.azure.storage.file.datalake.models.PathPermissions;
    ```

4. Rufen Sie mit dem folgenden Codeausschnitt in Ihrer Java-App ein Token für die Active Directory-Web-App ab, die Sie zuvor mit einer der Klassen von `StorageSharedKeyCredential` erstellt haben (Im folgenden Beispiel wird `credential` verwendet). Der Tokenanbieter speichert die verwendeten Anmeldeinformationen zwischen, um das Token im Arbeitsspeicher abzurufen, und erneuert das Token automatisch, bevor es abläuft. Sie können zwar auch eigene Unterklassen von `StorageSharedKeyCredential` erstellen, damit Token durch Ihren Kundencode abgerufen werden. Wir verwenden jedoch fürs Erste die Unterklasse aus dem SDK.

    Ersetzen Sie **FILL-IN-HERE** durch die Werte für die Azure Active Directory-Webanwendung.

    ```java
    private static String clienttId = "FILL-IN-HERE";
    private static String tenantId = "FILL-IN-HERE";
    private static String clientSecret = "FILL-IN-HERE";
   
    ClientSecretCredential credential = new ClientSecretCredentialBuilder().clientId(clientId).tenantId(tenantId).clientSecret(clientSecret).build();
    ```

Das Data Lake Storage Gen2 SDK ermöglicht die komfortable Verwaltung der Sicherheitstoken, die für die Kommunikation mit dem Data Lake Storage Gen2-Konto benötigt werden. Das SDK ermöglicht jedoch auch die Verwendung anderer Methoden. Token können daher auch auf andere Weise abgerufen werden – etwa mit der [Azure-Identitätsclientbibliothek](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/identity/azure-identity) oder mit eigenem benutzerdefiniertem Code.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Authentifizierung von Endbenutzer*innen verwenden, um sich mithilfe des Java SDK bei Data Lake Storage Gen2 zu authentifizieren. In den folgenden Artikeln wird erörtert, wie Sie das Java SDK mit Data Lake Storage Gen2 verwenden.

* [Datenvorgänge in Data Lake Storage Gen2 mit dem Java SDK](data-lake-store-get-started-java-sdk.md)
