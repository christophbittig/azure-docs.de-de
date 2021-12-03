---
title: 'Tutorial: Bereitstellen einer Spring Boot-Anwendung in einem AKS-Cluster mit MySQL Flexible Server in einem VNet'
description: Hier erfahren Sie, wie Sie schnell eine Spring Boot-Anwendung in AKS mit Azure Database for MySQL – Flexible Server mit sicherer Konnektivität in einem VNet erstellen und bereitstellen.
ms.service: mysql
author: shreyaaithal
ms.author: shaithal
ms.topic: tutorial
ms.date: 11/11/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 8b07ffd9ea4d9cbcba5cf36bcac048f1abccee37
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132557482"
---
# <a name="tutorial-deploy-a-spring-boot-application-on-aks-cluster-with-mysql-flexible-server-in-a-vnet"></a>Tutorial: Bereitstellen einer Spring Boot-Anwendung in einem AKS-Cluster mit MySQL Flexible Server in einem VNet

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Tutorial erfahren Sie, wie Sie eine [Spring Boot-Anwendung](https://spring.io/projects/spring-boot) in einem [Azure Kubernetes Service-Cluster (AKS)](../../aks/intro-kubernetes.md) mit [Azure Database for MySQL – Flexible Server](overview.md) im Back-End bereitstellen und dabei sicherstellen, dass alle Komponenten in einem [virtuellen Azure-Netzwerk](../../virtual-network/virtual-networks-overview.md) sicher miteinander kommunizieren. 

> [!NOTE]
> In diesem Tutorial werden grundlegende Kenntnisse von Kubernetes-Konzepten, Java Spring Boot und MySQL vorausgesetzt.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/install-azure-cli)
- Ein unterstütztes [Java Development Kit](/azure/developer/java/fundamentals/java-support-on-azure), Version 8 (in Azure Cloud Shell enthalten).
- Das [Apache Maven](https://maven.apache.org/)-Buildtool
- Einen [Git-Client](https://github.com/)
- Einen [Docker](https://www.docker.com/)-Client

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Erstellen einer Instanz von Azure Database for MySQL – Flexible Server 

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe
Eine Azure-Ressourcengruppe ist eine logische Gruppe, in der Azure-Ressourcen bereitgestellt und verwaltet werden. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe namens *rg-mysqlaksdemo* am Standort *eastus*.

1. Öffnen Sie die Eingabeaufforderung. 
1. Melden Sie sich bei Ihrem Azure-Konto an.
    ```azurecli-interactive
    az login
    ```
1. Wählen Sie Ihr Azure-Abonnement aus.
    ```azurecli-interactive
    az account set -s <your-subscription-ID>
    ```    
1. Erstellen Sie die Ressourcengruppe.
    ```azurecli-interactive
    az group create --name rg-mysqlaksdemo --location eastus
    ```

### <a name="create-a-mysql-flexible-server"></a>Erstellen einer MySQL Flexible Server-Instanz

Sie erstellen nun eine Flexible Server-Instanz in einem virtuellen Netzwerk (Methode für private Zugriffskonnektivität).

1. Erstellen Sie das virtuelle Azure-Netzwerk *vnet-mysqlaksdemo* für alle Ressourcen in diesem Tutorial sowie das Subnetz *subnet-mysql* für die MySQL Flexible Server-Instanz.

    ```azurecli-interactive
    az network vnet create \
    --resource-group rg-mysqlaksdemo \
    --name vnet-mysqlaksdemo \
    --address-prefixes 155.55.0.0/16 \
    --subnet-name subnet-mysql \
    --subnet-prefix 155.55.1.0/24 
    ```

1. Erstellen Sie eine Instanz von Azure Database for MySQL – Flexible Server namens *mysql-mysqlaksdemo* in dem oben erstellten Subnetz. Verwenden Sie dazu den Befehl [az mysql flexible-server create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create). Ersetzen Sie Ihre Werte für Administratorbenutzername und -kennwort.

    ```azurecli-interactive
    az mysql flexible-server create \
    --name mysql-mysqlaksdemo \
    --resource-group rg-mysqlaksdemo \
    --location eastus \
    --admin-user <your-admin-username> \
    --admin-password <your-admin-password> \
    --vnet vnet-mysqlaksdemo \
    --subnet subnet-mysql
    ```
    
    Sie haben nun in der Region „eastus“ im angegebenen Subnetz *subnet-mysql* eine Flexible Server-Instanz mit einer burstfähigen B1MS-Computeressource, 32 GB Speicher und einer Aufbewahrungsdauer von sieben Tagen für Sicherungen erstellt. Dieses Subnetz sollte keine anderen Ressourcen enthalten, und es wird an „Microsoft.DBforMySQL/flexibleServers“ delegiert.

1. Konfigurieren Sie die neue MySQL-Datenbank ```demo``` für die Verwendung mit der Spring Boot-Anwendung.

    ```azurecli-interactive
    az mysql flexible-server db create \
    --resource-group rg-mysqlaksdemo \
    --server-name mysql-mysqlaksdemo \
    --database-name demo
    ```

## <a name="create-an-azure-container-registry"></a>Erstellen einer Azure-Containerregistrierung 

Erstellen Sie eine private Azure Container Registry-Instanz in der Ressourcengruppe. Die Beispiel-App wird in diesem Tutorial in späteren Schritten als Docker-Image per Push in diese Registrierung übertragen. Ersetzen Sie ```mysqlaksdemoregistry``` durch einen eindeutigen Namen für die Registrierung.

```azurecli-interactive
az acr create --resource-group rg-mysqlaksdemo \
--location eastus \
--name mysqlaksdemoregistry \
--sku Basic
```

## <a name="code-the-application"></a>Codieren der Anwendung

In diesem Abschnitt wird die Demoanwendung programmiert. Wenn Sie schneller vorgehen möchten, können Sie die programmierte Anwendung herunterladen, die unter [https://github.com/Azure-Samples/tutorial-springboot-mysql-aks](https://github.com/Azure-Samples/tutorial-springboot-mysql-aks) verfügbar ist, und mit dem nächsten Abschnitt fortfahren: [Erstellen des Images und Pushen an ACR](#build-the-image-and-push-to-acr). 

1. Erstellen Sie die Anwendung mit Spring Initializr: 

    ```bash
    curl https://start.spring.io/starter.tgz \
    -d dependencies=web,data-jdbc,mysql \
    -d baseDir=springboot-mysql-aks \
    -d bootVersion=2.5.6.RELEASE \
    -d artifactId=springboot-mysql-aks \
    -d description="Spring Boot on AKS connecting to Azure DB for MySQL" \
    -d javaVersion=1.8 | tar -xzvf -
    ```
    
    Eine Spring Boot-Basisanwendung wird im Ordner ```springboot-mysql-aks``` generiert.
    
    Verwenden Sie für die folgenden Schritte Ihren bevorzugten Text-Editor (etwa [VSCode](https://code.visualstudio.com/docs)) oder eine beliebige IDE.

1. Konfigurieren Sie Spring Boot für die Verwendung von Azure Database for MySQL – Flexible Server.

    Öffnen Sie die Datei „src/main/resources/application.properties“, und fügen Sie den folgenden Codeausschnitt hinzu. Dieser Code liest den Datenbankhost, den Datenbanknamen, den Benutzernamen und das Kennwort aus der Kubernetes-Manifestdatei.
    
    ```properties
    logging.level.org.springframework.jdbc.core=DEBUG
    spring.datasource.url=jdbc:mysql://${DATABASE_HOST}:3306/${DATABASE_NAME}?serverTimezone=UTC
    spring.datasource.username=${DATABASE_USERNAME}
    spring.datasource.password=${DATABASE_PASSWORD}
    spring.datasource.initialization-mode=always
    ```
    >[!Warning]
    > Die Konfigurationseigenschaft ```spring.datasource.initialization-mode=always``` bedeutet, dass Spring Boot mithilfe der Datei ```schema.sql```, die Sie später erstellen, bei jedem Start des Servers automatisch ein Datenbankschema generiert. Dies eignet sich hervorragend für Tests. Denken Sie jedoch daran, dass Ihre Daten bei jedem Neustart gelöscht werden, daher sollte diese Eigenschaft nicht in der Produktion verwendet werden.

    >[!Note]
    >Sie fügen ```?serverTimezone=UTC``` an die Konfigurationseigenschaft ```spring.datasource.url``` an, um den JDBC-Treiber anzuweisen, beim Herstellen einer Verbindung mit der Datenbank das Datumsformat UTC (Coordinated Universal Time, koordinierte Weltzeit) zu verwenden. Andernfalls verwendet Ihr Java-Server nicht das gleiche Datumsformat wie die Datenbank, was zu einem Fehler führen würde.

1. Erstellen Sie das Datenbankschema.
    
    Spring Boot führt automatisch ```src/main/resources/schema.sql``` aus, um ein Datenbankschema zu erstellen. Erstellen Sie diese Datei mit folgendem Inhalt:

    ```sql
    DROP TABLE IF EXISTS todo;
    CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
    ```
1. Programmieren Sie die Java Spring Boot-Anwendung.
    
    Fügen Sie den Java-Code hinzu, der JDBC zum Speichern und Abrufen von Daten auf Ihrem MySQL-Server verwendet. Erstellen Sie neben der Klasse ```DemoApplication``` eine neue ```Todo```-Java-Klasse, und fügen Sie den folgenden Code hinzu:

    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.annotation.Id;
    
    public class Todo {
    
        public Todo() {
        }
    
        public Todo(String description, String details, boolean done) {
            this.description = description;
            this.details = details;
            this.done = done;
        }
    
        @Id
        private Long id;
    
        private String description;
    
        private String details;
    
        private boolean done;
    
        public Long getId() {
            return id;
        }
    
        public void setId(Long id) {
            this.id = id;
        }
    
        public String getDescription() {
            return description;
        }
    
        public void setDescription(String description) {
            this.description = description;
        }
    
        public String getDetails() {
            return details;
        }
    
        public void setDetails(String details) {
            this.details = details;
        }
    
        public boolean isDone() {
            return done;
        }
    
        public void setDone(boolean done) {
            this.done = done;
        }
    }
    ```
    Bei dieser Klasse handelt es sich um ein Domänenmodell, das der zuvor erstellten ```todo```-Tabelle zugeordnet ist.

    Für die Verwaltung dieser Klasse ist ein Repository erforderlich. Definieren Sie eine neue ```TodoRepository```-Schnittstelle im gleichen Paket:
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.data.repository.CrudRepository;
    
    public interface TodoRepository extends CrudRepository<Todo, Long> {
    }
    ```

    Dieses Repository wird von Spring Data-JDBC verwaltet.

    Stellen Sie die Anwendung fertig, indem Sie einen Controller erstellen, der Daten speichern und abrufen kann. Implementieren Sie eine ```TodoController```-Klasse im gleichen Paket, und fügen Sie den folgenden Code hinzu:
    
    ```java
    package com.example.springbootmysqlaks;

    import org.springframework.http.HttpStatus;
    import org.springframework.web.bind.annotation.*;
    
    @RestController
    @RequestMapping("/")
    public class TodoController {
    
        private final TodoRepository todoRepository;
    
        public TodoController(TodoRepository todoRepository) {
            this.todoRepository = todoRepository;
        }
    
        @PostMapping("/")
        @ResponseStatus(HttpStatus.CREATED)
        public Todo createTodo(@RequestBody Todo todo) {
            return todoRepository.save(todo);
        }
    
        @GetMapping("/")
        public Iterable<Todo> getTodos() {
            return todoRepository.findAll();
        }
    }
    ```

1. Erstellen Sie eine neue Dockerfile-Datei im Basisverzeichnis *springboot-mysql-aks*, und kopieren Sie den folgenden Codeausschnitt:

    ```dockerfile
    FROM openjdk:8-jdk-alpine
    RUN addgroup -S spring && adduser -S spring -G spring
    USER spring:spring
    ARG DEPENDENCY=target/dependency
    COPY ${DEPENDENCY}/BOOT-INF/lib /app/lib
    COPY ${DEPENDENCY}/META-INF /app/META-INF
    COPY ${DEPENDENCY}/BOOT-INF/classes /app
    ENTRYPOINT ["java","-cp","app:app/lib/*","com.example.springbootmysqlaks.DemoApplication"]
    ```

1. Navigieren Sie zur Datei *pom.xml*, und aktualisieren Sie die Sammlung ```<properties>``` in der Datei „pom.xml“ mit dem Registrierungsnamen für Ihre Azure Container Registry-Instanz und der aktuellen Version von ```jib-maven-plugin```.
    Hinweis: Wenn Ihr ACR-Name Großbuchstaben enthält, ändern Sie diese unbedingt in Kleinbuchstaben.

    ```xml
    <properties>
        <docker.image.prefix>mysqlaksdemoregistry.azurecr.io</docker.image.prefix>
        <jib-maven-plugin.version>3.1.4</jib-maven-plugin.version>
        <java.version>1.8</java.version>
    </properties>
    ```

1. Aktualisieren Sie die Sammlung ```<plugins>``` in der Datei *pom.xml*, sodass das ```<plugin>```-Element einen Eintrag für ```jib-maven-plugin``` enthält, wie im Beispiel unten gezeigt. Beachten Sie, dass ein Basisimage aus der Microsoft Container Registry (MCR) „```mcr.microsoft.com/java/jdk:8-zulu-alpine```“ verwendet wird, das eine offiziell unterstützte JDK-Version für Azure enthält. Informationen zu anderen MCR-Basisimages mit offiziell unterstützten JDK-Versionen finden Sie unter [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) und [Java SE JDK and Maven](https://hub.docker.com/_/microsoft-java-maven).
    
    ```xml
    <plugin>
        <artifactId>jib-maven-plugin</artifactId>
        <groupId>com.google.cloud.tools</groupId>
        <version>${jib-maven-plugin.version}</version>
        <configuration>
            <from>
                <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
            </from>
            <to>
                <image>${docker.image.prefix}/${project.artifactId}</image>
            </to>
        </configuration>
    </plugin>
    ```

## <a name="build-the-image-and-push-to-acr"></a>Erstellen des Images und Pushen an ACR

Navigieren Sie an der Eingabeaufforderung zum Ordner *springboot-mysql-aks*, und führen Sie die folgenden Befehle aus, um zuerst den Standardnamen für Azure Container Registry festzulegen, (andernfalls müssen Sie den Namen in ```az acr login``` angeben), erstellen Sie das Image, und pushen Sie das Image dann in die Registrierung.

Stellen Sie sicher, dass Ihr Docker-Daemon während dieses Schritts ausgeführt wird.

```azurecli-interactive
az config set defaults.acr=mysqlaksdemoregistry
az acr login && mvn compile jib:build
```

## <a name="create-a-kubernetes-cluster-on-aks"></a>Erstellen eines Kubernetes-Clusters in AKS

Sie erstellen nun einen AKS-Cluster im virtuellen Netzwerk *vnet-mysqlaksdemo*. 

In diesem Tutorial verwenden Sie das Azure CNI-Netzwerk in AKS. Wenn Sie stattdessen ein Kubenet-Netzwerk konfigurieren möchten, finden Sie weitere Informationen unter [Verwenden von kubenet-Netzwerken mit Ihren eigenen IP-Adressbereichen in Azure Kubernetes Service (AKS)](../../aks/configure-kubenet.md#create-a-service-principal-and-assign-permissions).  

1. Erstellen Sie das Subnetz *subnet-aks*, das vom AKS-Cluster verwendet werden soll.

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group rg-mysqlaksdemo \
    --vnet-name vnet-mysqlaksdemo \
    --name subnet-aks \
    --address-prefixes 155.55.2.0/24
    ```

1. Rufen Sie die Subnetzressourcen-ID ab.

    ```azurecli-interactive
    SUBNET_ID=$(az network vnet subnet show --resource-group rg-mysqlaksdemo --vnet-name vnet-mysqlaksdemo --name subnet-aks --query id -o tsv)
    ```
    
1. Erstellen Sie einen AKS-Cluster im virtuellen Netzwerk mit der angefügten Azure Container Registry-Instanz (ACR) *mysqlaksdemoregistry*.

    ```azurecli-interactive
        az aks create \
        --resource-group rg-mysqlaksdemo \
        --name aks-mysqlaksdemo \
        --network-plugin azure \
        --service-cidr 10.0.0.0/16 \
        --dns-service-ip 10.0.0.10 \
        --docker-bridge-address 172.17.0.1/16 \
        --vnet-subnet-id $SUBNET_ID \
        --attach-acr mysqlaksdemoregistry \
        --dns-name-prefix aks-mysqlaksdemo \
        --generate-ssh-keys
    ```

    Die folgenden IP-Adressbereiche sind auch als Teil des Clustererstellungsprozesses definiert:
    
    - Mit *--service-cidr* werden interne Dienste im AKS-Cluster einer IP-Adresse zugewiesen. Sie können jeden privaten Adressbereich verwenden, der die folgenden Anforderungen erfüllen:
        - Darf nicht innerhalb des IP-Adressbereichs des virtuellen Netzwerk Ihres Clusters liegen
        - Darf sich nicht mit anderen virtuellen Netzwerken überlappen, die Peers des virtuellen Netzwerks des Clusters sind
        - Er darf sich nicht mit lokalen IP-Adressen überlappen.
        - Er darf sich nicht in den Bereichen 169.254.0.0/16, 172.30.0.0/16, 172.31.0.0/16 oder 192.0.2.0/24 befinden.
    
    - Die Adresse *--dns-service-ip* ist die IP-Adresse für den DNS-Dienst des Clusters. Diese Adresse muss innerhalb des *Kubernetes-Dienstadressbereichs* liegen. Verwenden Sie nicht die erste IP-Adresse Ihres Adressbereichs. Die erste Adresse Ihres Subnetzbereichs wird für die Adresse *kubernetes.default.svc.cluster.local* genutzt.
    
    - *--docker-bridge-address* ist die Netzwerkadresse der Docker-Brücke, die die Standardnetzwerkadresse der Brücke *docker0* darstellt, die in allen Docker-Installationen vorhanden ist. Wählen Sie einen Adressraum aus, der nicht mit den übrigen CIDRs in Ihrem Netzwerk in Konflikt steht (einschließlich Dienst-CIDR und Pod-CIDR des Clusters).

## <a name="deploy-the-application-to-aks-cluster"></a>Bereitstellen der Anwendung einem AKS-Cluster

1. Navigieren Sie im Azure-Portal zu Ihrer AKS-Clusterressource.

1. Wählen Sie in einer der Ressourcenansichten („Namespace“, „Workloads“, „Dienste und Eingänge“, „Speicher“ oder „Konfiguration“) die Optionen **Hinzuzufügen** und **Add with YAML** (Mit YAML hinzufügen) aus. 

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-resource-blade.png" alt-text="Screenshot: Azure Kubernetes Service-Ressourcenansicht im Azure-Portal":::

1. Fügen Sie den folgenden YAML-Code ein. Fügen Sie Ihre Werte für den Administratorbenutzernamen und das Kennwort für MySQL Flexible Server ein.

    ```yml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: springboot-mysql-aks
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: springboot-mysql-aks
      template:
        metadata:
          labels:
            app: springboot-mysql-aks
        spec:
          containers:
          - name: springboot-mysql-aks
            image: mysqlaksdemoregistry.azurecr.io/springboot-mysql-aks:latest
            env:
            - name: DATABASE_HOST
              value: "mysql-mysqlaksdemo.mysql.database.azure.com"
            - name: DATABASE_USERNAME
              value: "<your-admin-username>"
            - name: DATABASE_PASSWORD
              value: "<your-admin-password>"
            - name: DATABASE_NAME    
              value: "demo"     
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: springboot-mysql-aks
    spec:
      type: LoadBalancer
      ports:
      - port: 80
        targetPort: 8080
      selector:
        app: springboot-mysql-aks
    ```
1. Wählen Sie unten im YAML-Editor **Hinzufügen** aus, um die Anwendung bereitzustellen.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-add-with-yaml.png" alt-text="Screenshot: Editor zum Hinzufügen mit YAML":::

1. Nachdem die YAML-Datei hinzugefügt wurde, wird Ihre Spring Boot-Anwendung in der Ressourcenansicht angezeigt. Notieren Sie sich die verknüpfte externe IP-Adresse, die im externen Dienst enthalten ist.

    :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-external-ip.png" alt-text="Screenshot: Ansicht der externen IP-Adresse des Azure Kubernetes-Clusterdiensts im Azure-Portal":::

## <a name="test-the-application"></a>Testen der Anwendung

Zum Testen der Anwendung können Sie cURL verwenden.

Erstellen Sie zunächst mit dem folgenden Befehl ein neues todo-Element in der Datenbank:

```bash
curl --header "Content-Type: application/json" \
--request POST \
--data '{"description":"configuration","details":"congratulations, you have deployed your application correctly!","done": "true"}' \
http://<AKS-service-external-ip>
```

Rufen Sie als Nächstes die Daten mithilfe einer neuen cURL-Anforderung oder durch Eingabe der *externen IP-Adresse* des Clusters im Browser ab.

```bash
curl http://<AKS-service-external-ip>
```

Mit dem folgenden Befehl wird die Liste der todo-Elemente zurückgegeben, einschließlich des von Ihnen erstellten Elements:

```json
[{"id":1,"description":"configuration","details":"congratulations, you have deployed your application correctly!","done":true}]
```

Hier sehen Sie einen Screenshot dieser cURL-Anforderungen: :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-curl-output.png" alt-text="Screenshot: Befehlszeilenausgabe von cURL-Anforderungen":::

Im Browser sehen Sie eine ähnliche Ausgabe: :::image type="content" source="media/tutorial-deploy-springboot-on-aks-vnet.md/aks-browser-output.png" alt-text="Screenshot: Anforderungsausgabe im Browser":::


Herzlichen Glückwunsch! Sie haben erfolgreich eine Spring Boot-Anwendung im Azure Kubernetes Service-Cluster (AKS) mit Azure Database for MySQL – Flexible Server im Back-End bereitgestellt. 


## <a name="clean-up-the-resources"></a>Bereinigen der Ressourcen

Zum Vermeiden von Azure-Gebühren sollten Sie nicht benötigte Ressourcen bereinigen.  Wenn der Cluster nicht mehr benötigt wird, entfernen Sie mit dem Befehl [az group delete](/cli/azure/group#az_group_delete) die Ressourcengruppe, den Containerdienst und alle zugehörigen Ressourcen.

```azurecli-interactive
az group delete --name rg-mysqlaksdemo
```

> [!NOTE]
> Wenn Sie den Cluster löschen, wird der vom AKS-Cluster verwendete Azure Active Directory-Dienstprinzipal nicht entfernt. Schritte zum Entfernen des Dienstprinzipals finden Sie unter den [Überlegungen zum AKS-Dienstprinzipal und dessen Löschung](../../aks/kubernetes-service-principal.md#additional-considerations). Wenn Sie eine verwaltete Identität verwendet haben, wird die Identität von der Plattform verwaltet und muss nicht entfernt werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen einer WordPress-App in AKS mit MySQL](tutorial-deploy-wordpress-on-aks.md)

> [!div class="nextstepaction"]
> [Erstellen einer PHP-Web-App (Laravel) mit MySQL](tutorial-php-database-app.md)


