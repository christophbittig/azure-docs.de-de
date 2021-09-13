---
title: Azure Private Link für Azure Data Factory
description: Erfahren Sie etwas über die Funktionsweise von Azure Private Link in Azure Data Factory.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f4ae3d0653ce99be2017b1ef08ca0645c599ee10
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339739"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link für Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Mithilfe von Azure Private Link können Sie eine Verbindung mit verschiedenen PaaS-Bereitstellungen (Platform-as-a-Service) in Azure über einen privaten Endpunkt herstellen. Ein privater Endpunkt ist eine private IP-Adresse in einem bestimmten virtuellen Netzwerk und Subnetz. Eine Liste der PaaS-Bereitstellungen, die die Private Link-Funktionalität unterstützen, finden Sie in der [Dokumentation zu Private Link](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Sichere Kommunikation zwischen dem Kundennetzwerk und Azure Data Factory 
Sie können Azure Virtual Network als logische Darstellung Ihres Netzwerks in der Cloud einrichten. Diese Vorgehensweise bietet folgende Vorteile:
* Sie schützen damit Ihre Azure-Ressourcen vor Angriffen in öffentlichen Netzwerken.
* Sie ermöglichen eine sichere Kommunikation zwischen den Netzwerken und Data Factory. 

Sie können ein lokales Netzwerk auch mit Ihrem virtuellen Netzwerk verbinden, indem Sie eine IPsec-VPN-Verbindung (Internetprotokollsicherheit, Site-to-Site) oder eine Azure ExpressRoute-Verbindung (privates Peering) einrichten. 

Sie können auch eine selbstgehostete Integration Runtime auf einem lokalen Computer oder einem virtuellen Computer im virtuellen Netzwerk installieren. Dies ermöglicht Folgendes:
* Ausführen von Kopieraktivitäten zwischen einem Clouddatenspeicher und einem Datenspeicher im privaten Netzwerk
* Verteilen von Transformationsaktivitäten für Computeressourcen in einem lokalen Netzwerk oder einem virtuellen Azure-Netzwerk 

Zwischen Azure Data Factory und dem virtuellen Netzwerk des Kunden sind mehrere Kommunikationskanäle erforderlich, wie in der folgenden Tabelle dargestellt:

| Domain | Port | BESCHREIBUNG |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Eine Steuerungsebene, erforderlich für die Data Factory-Erstellung und -Überwachung |
| `*.{region}.datafactory.azure.net` | 443 | Erforderlich für die selbstgehostete Integration Runtime, um Verbindungen mit dem Azure Data Factory-Dienst herzustellen. |
| `*.servicebus.windows.net` | 443 | Erforderlich für die selbstgehostete Integration Runtime zur interaktiven Erstellung |
| `download.microsoft.com` | 443 | Erforderlich für die selbstgehostete Integration Runtime zum Herunterladen der Aktualisierungen. |

Mit der Unterstützung von Private Link für Azure Data Factory können Sie folgende Aktionen ausführen:
* Erstellen eines privaten Endpunkts in Ihrem virtuellen Netzwerk
* Aktivieren einer privaten Verbindung mit einer bestimmten Data Factory-Instanz 

Die Kommunikation mit dem Azure Data Factory-Dienst durchläuft Private Link, sodass eine geschützte private Konnektivität zur Verfügung steht. 

:::image type="content" source="./media/data-factory-private-link/private-link-architecture.png" alt-text="Diagramm von Private Link für die Azure Data Factory-Architektur":::

Die Aktivierung des Private Link-Diensts für die genannten Kommunikationskanäle bietet die folgenden Funktionen:
- **Unterstützt:**
   - Sie können Erstellungs- und Überwachungsvorgänge der Data Factory in Ihrem virtuellen Netzwerk ausführen, selbst wenn Sie die gesamte ausgehende Kommunikation blockieren.
   - Die Befehlskommunikation zwischen einer selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst kann sicher in einer privaten Netzwerkumgebung stattfinden. Der Datenverkehr zwischen der selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst erfolgt über Private Link. 
- **Derzeit nicht unterstützt:**
   - Die interaktive Erstellung mithilfe der selbstgehosteten Integration Runtime durchläuft Private Link, z. B. für eine Testverbindung, das Durchsuchen der Ordner- und Tabellenliste, das Abrufen eines Schemas und das Anzeigen einer Vorschau von Daten.
   - Die neue Version der selbstgehosteten Integration Runtime, die automatisch aus dem Microsoft Download Center heruntergeladen werden kann, wenn Sie die automatische Aktualisierung aktivieren, wird zurzeit nicht unterstützt.

   > [!NOTE]
   > Für Funktionen, die derzeit nicht unterstützt werden, müssen Sie die oben genannte Domäne und den Port weiterhin im virtuellen Netzwerk oder der Unternehmensfirewall konfigurieren. 

   > [!NOTE]
   > Das Herstellen einer Verbindung mit Azure Data Factory über einen privaten Endpunkt gilt nur für eine selbstgehostete Integration Runtime in Data Factory. Für Azure Synapse wird dies nicht unterstützt.

> [!WARNING]
> Wenn Sie Private Link in Azure Data Factory aktivieren und gleichzeitig den öffentlichen Zugriff blockieren, müssen Sie beim Erstellen eines verknüpften Diensts sicherstellen, dass Ihre Anmeldeinformationen in einem Azure-Schlüsseltresor gespeichert sind. Anderenfalls funktionieren die Anmeldeinformationen nicht.

## <a name="dns-changes-for-private-endpoints"></a>DNS-Änderungen für private Endpunkte
Wenn Sie einen privaten Endpunkt erstellen, wird der DNS-CNAME-Ressourceneintrag für die Data Factory auf einen Alias in einer Unterdomäne mit dem Präfix „privatelink“ aktualisiert. Standardmäßig erstellen wir außerdem eine [private DNS-Zone](../dns/private-dns-overview.md), die der Unterdomäne „privatelink“ entspricht, mit den DNS-A-Ressourceneinträgen für die privaten Endpunkte.

Wenn Sie die Endpunkt-URL der Data Factory außerhalb des VNET mit dem privaten Endpunkt auflösen, wird diese in den öffentlichen Endpunkt des Data Factory-Diensts aufgelöst. Bei Auflösung aus dem VNET, das den privaten Endpunkt hostet, wird die Speicherendpunkt-URL in die IP-Adresse des privaten Endpunkts aufgelöst.

Beim oben gezeigten Beispiel lauten die DNS-Ressourceneinträge für die Data Factory „DataFactoryA“ bei Auflösung von außerhalb des VNET, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net | CNAME   | < öffentlicher Endpunkt des Data Factory-Diensts > |
| < öffentlicher Endpunkt des Data Factory-Diensts >  | Ein | < öffentliche IP-Adresse des Data Factory-Diensts > |

Die DNS-Ressourceneinträge für „DataFactoryA“ lauten nach dem Auflösen im VNET, das den privaten Endpunkt hostet, wie folgt:

| Name | type | Wert |
| ---------- | -------- | --------------- |
| DataFactoryA.{region}.datafactory.azure.net | CNAME   | DataFactoryA.{region}.privatelink.datafactory.azure.net |
| DataFactoryA.{region}.privatelink.datafactory.azure.net   | Ein | < IP-Adresse des privaten Endpunkts > |

Wenn Sie einen benutzerdefinierten DNS-Server in Ihrem Netzwerk verwenden, müssen Clients in der Lage sein, den FQDN für den Data Factory-Endpunkt in die IP-Adresse des privaten Endpunkts aufzulösen. Sie sollten den DNS-Server so konfigurieren, dass die Unterdomäne der privaten Verbindung an die private DNS-Zone für das VNET delegiert wird, oder konfigurieren Sie die A-Einträge für „DataFactoryA.{region}.privatelink.datafactory.azure.net“ mit der IP-Adresse des privaten Endpunkts.

Weitere Informationen zum Konfigurieren des eigenen DNS-Servers für die Unterstützung privater Endpunkte finden Sie in den folgenden Artikeln:
- [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-Konfiguration für private Endpunkte](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-a-private-endpoint-link-for-azure-data-factory"></a>Einrichten eines privaten Endpunkts für Azure Data Factory

In diesem Abschnitt richten Sie einen Link für einen privaten Endpunkt für Azure Data Factory ein.

Sie können auswählen, ob Sie ihre selbstgehosteten Integration Runtime (SHIR) während des Data Factory-Erstellungsschritts Azure Data Factory einem öffentlichen oder einem privaten Endpunkt mit Azure Data Factory verbinden möchten: 

:::image type="content" source="./media/data-factory-private-link/disable-public-access-shir.png" alt-text="Screenshot des Blockierens des öffentlichen Zugriffs der selbstgehosteten Integration Runtime":::

Sie können die Auswahl jederzeit nach der Erstellung auf der Data Factory-Portalseite auf dem Blatt Netzwerk ändern.  Nachdem Sie dort private Endpunkte aktiviert haben, müssen Sie der Data Factory auch einen privaten Endpunkt hinzufügen.

Ein privater Endpunkt erfordert ein virtuelles Netzwerk und Subnetz für den Link sowie einen virtuellen Computer innerhalb des Subnetzes, der zum Ausführen der selbstgehosteten Integration Runtime (SHIR) verwendet wird und eine Verbindung über den privaten Endpunktlink herstellen soll.

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks
Wenn Sie nicht über ein vorhandenes virtuelles Netzwerk verfügen, das mit Ihrem privaten Endpunktlink verwendet werden kann, müssen Sie eines erstellen und ein Subnetz zuweisen.  

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.
2. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

3. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Auswählen einer Ressourcengruppe für Ihr virtuelles Netzwerk |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie einen Namen für Ihr virtuelles Netzwerk ein. |
    | Region           | WICHTIG: Wählen Sie dieselbe Region aus, die auch Ihr privater Endpunkt verwenden wird. |

4. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

5. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

6. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

7. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie einen Namen für Ihr Subnetz ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

8. Wählen Sie **Speichern** aus.

9. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

10. Klicken Sie auf **Erstellen**.

### <a name="create-a-virtual-machine-for-the-self-hosted-integration-runtime-shir"></a>Erstellen eines virtuellen Computers für die selbstgehostete Integration Runtime (SHIR)
Sie müssen auch einen virtuellen Computer erstellen oder eine vorhandene VM zuweisen, um die selbstgehostete Integration Runtime im oben erstellten neuen Subnetz ausführen zu können.

1. Wählen Sie oben links im Portal die Option **Ressource erstellen** > **Compute** > **Virtueller Computer** aus, oder suchen Sie über das Suchfeld nach **Virtueller Computer**.
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Auswählen einer Ressourcengruppe |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie einen Namen für den virtuellen Computer an. |
    | Region | Wählen Sie die oben verwendete Region für Ihr virtuelles Netzwerk aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter – Gen1** (oder ein anderes Windows-Image, das die selbstgehostete Integration Runtime unterstützt) aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Wählen Sie das virtuelle Netzwerk aus, das Sie zuvor erstellt haben. |
    | Subnet | Wählen Sie das zuvor erstellte Subnetz aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | **Grundlegend**|
    | Öffentliche Eingangsports | Wählen Sie **Keine**. |
   
5. Klicken Sie auf **Überprüfen + erstellen**. 
  
6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

### <a name="create-the-private-endpoint"></a>Erstellen des privaten Endpunkts 
Abschließend müssen Sie den privaten Endpunkt in Ihrer Data Factory erstellen.

1. Wählen Sie die Azure-Portal-Seite für Ihre Data Factory aus, wählen Sie das Blatt **Netzwerk** und die Registerkarte **Private Endpunktverbindungen** aus, und wählen Sie dann **+ Privater Endpunkt** aus. 

:::image type="content" source="./media/data-factory-private-link/create-private-endpoint.png" alt-text="Screenshot des Bereichs „Private Endpunktverbindungen“ zum Erstellen eines privaten Endpunkts":::

2. Geben Sie auf der Registerkarte **Grundlagen** unter **Privaten Endpunkt erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Projektdetails** | |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Resource group | Auswählen einer Ressourcengruppe |
    | **Instanzendetails** |  |
    | Name  | Namen für den Endpunkt eingeben |
    | Region | Wählen Sie die Region des oben erstellten virtuellen Netzwerks aus. |

3. Wählen Sie die Registerkarte **Ressource** oder unten auf der Seite **Weiter: Ressource** aus.
    
4. Geben Sie in **Ressource** diese Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsmethode | Wählen Sie **Mit einer Azure-Ressource in meinem Verzeichnis verbinden** aus. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Ressourcentyp | Wählen Sie **Microsoft.DataFactory/factories** aus: |
    | Ressource | Data Factory auswählen |
    | Zielunterressource | Wenn Sie einen privaten Endpunkt für die Befehlskommunikation zwischen der selbstgehosteten Integration Runtime und dem Azure Data Factory-Dienst nutzen möchten, wählen Sie für **Zielunterressource** den Eintrag **datafactory** aus. Wenn Sie einen privaten Endpunkt für die Erstellung und Überwachung der Data Factory in Ihrem virtuellen Netzwerk nutzen möchten, wählen Sie für **Zielunterressource** den Eintrag **portal** aus.|

5. Wählen Sie die Registerkarte **Konfiguration** oder die Schaltfläche **Weiter: Konfiguration** am unteren Bildschirmrand aus.

6. Geben Sie diese Informationen in **Konfiguration** ein oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | **Netzwerk** |  |
    | Virtuelles Netzwerk | Wählen Sie das virtuelle Netzwerk aus, das Sie zuvor erstellt haben. |
    | Subnet | Wählen Sie das zuvor erstellte Subnetz aus. |
    | **Private DNS-Integration** |  |
    | Integration in eine private DNS-Zone | Übernehmen Sie den Standardwert **Ja**. |
    | Subscription | Wählen Sie Ihr Abonnement aus. |
    | Private DNS-Zonen | Belassen Sie den Standardwert **(New) privatelink.azurewebsites.net**.
    

7. Klicken Sie auf **Überprüfen + erstellen**.

8. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Die Deaktivierung des Zugriffs auf öffentliche Netzwerke gilt nur für die selbstgehostete Integration Runtime, nicht für Azure Integration Runtime und SSIS Integration Runtime (SQL Server Integration Services).

> [!NOTE]
> Sie können nach dem Erstellen eines privaten Endpunkts für das Portal weiterhin über ein öffentliches Netzwerk auf das Azure Data Factory-Portal zugreifen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Data Factory über die Azure Data Factory-Benutzeroberfläche](quickstart-create-data-factory-portal.md)
- [Einführung in den Azure Data Factory-Dienst](introduction.md)
- [Visuelles Erstellen in Azure Data Factory](author-visually.md)
