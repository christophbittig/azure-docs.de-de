---
title: Schützen des Datenverkehrs zwischen Workflows mit nur einem Mandanten und virtuellen Netzwerken
description: Schützen des Datenverkehr zwischen virtuellen Netzwerken, Speicherkonten und Workflows mit nur einem Mandanten in Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 500ec9c14dea994b528528389ed03d2b561c1a56
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241818"
---
# <a name="secure-traffic-between-virtual-networks-and-single-tenant-workflows-in-azure-logic-apps-using-private-endpoints"></a>Schützen des Datenverkehrs zwischen virtuellen Netzwerken und Workflows mit nur einem Mandanten in Azure Logic Apps mithilfe privater Endpunkte

Für eine sichere und private Kommunikation zwischen Ihrem Logik-App-Workflow und einem virtuellen Netzwerk können Sie *private Endpunkte* für eingehenden Datenverkehr einrichten und die Integration virtueller Netzwerke für ausgehenden Datenverkehr verwenden.

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die eine private und sichere Verbindung mit einem von Azure Private Link unterstützten Dienst herstellt. Bei diesem Dienst kann es sich um einen Azure-Dienst wie Azure Storage, Logic Apps, Azure Storage, Azure Cosmos DB, SQL oder um Ihren eigenen Private Link-Dienst handeln. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem virtuellen Netzwerk, was den Dienst effektiv in Ihr virtuelles Netzwerk einbindet.

In diesem Artikel erfahren Sie, wie Sie den Zugriff über private Endpunkte für eingehenden Datenverkehr, ausgehenden Datenverkehr und Verbindungen mit Speicherkonten einrichten.

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Was ist der private Azure-Endpunkt?](../private-link/private-endpoint-overview.md)

- [Was ist Azure Private Link?](../private-link/private-link-overview.md)

- [Was ist ein Logik-App-Workflow mit nur einem Mandanten in Azure Logic Apps?](single-tenant-overview-compare.md)

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein neues oder vorhandenes virtuelles Azure-Netzwerk, das ein Subnetz ohne Delegierungen umfasst. Dieses Subnetz wird verwendet, um private IP-Adressen aus einem virtuellen Netzwerk bereitzustellen und zuzuordnen.

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Schnellstart: Erstellen eines virtuellen Netzwerks im Azure-Portal](../virtual-network/quick-create-portal.md)

- [Was ist die Subnetzdelegierung?](../virtual-network/subnet-delegation-overview.md)

- [Hinzufügen oder Entfernen einer Subnetzdelegierung](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## <a name="set-up-inbound-traffic-through-private-endpoints"></a>Einrichten von eingehendem Datenverkehr über private Endpunkte

Führen Sie die folgenden allgemeinen Schritte aus, um bei Ihrem Workflow eingehenden Datenverkehr zu schützen:

1. Starten Sie Ihren Workflow mit einem integrierten Trigger, der eingehende Anforderungen empfangen und verarbeiten kann, z. B. dem Anforderungstrigger oder dem HTTP+Webhooktrigger. Dieser Trigger richtet Ihren Workflow mit einem aufrufbaren Endpunkt ein.

1. Fügen Sie Ihrem virtuellen Netzwerk einen privaten Endpunkt hinzu.

1. Führen Sie Testaufrufe durch, um den Zugriff auf den Endpunkt zu überprüfen. Um Ihren Logik-App-Workflow nach dem Einrichten dieses Endpunkts aufzurufen, müssen Sie mit dem virtuellen Netzwerk verbunden sein.

### <a name="prerequisites-for-inbound-traffic-through-private-endpoints"></a>Voraussetzungen für eingehenden Datenverkehr über private Endpunkte

Zusätzlich zur [Einrichtung des virtuellen Netzwerks in den allgemeinen Voraussetzungen](#prerequisites) benötigen Sie einen neuen oder vorhandenen, auf einem Mandanten basierenden Logik-App-Workflow, der mit einem integrierten Trigger startet, der Anforderungen empfangen kann.

Der Anforderungstrigger erstellt beispielsweise einen Endpunkt in Ihrem Workflow, der eingehende Anforderungen von anderen Aufrufern, einschließlich Workflows, empfangen und verarbeiten kann. Dieser Endpunkt stellt eine URL bereit, die Sie zum Aufrufen und Auslösen des Workflows verwenden können. In diesem Beispiel werden die Schritte unter Verwendung des Anforderungstriggers fortgesetzt.

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Erstellen von Logik-App-Workflows mit nur einem Mandanten in Azure Logic Apps](create-single-tenant-workflows-azure-portal.md)

- [Empfangen von und Reagieren auf eingehende HTTPS-Anforderungen mittels Azure Logic Apps](../connectors/connectors-native-reqres.md)

### <a name="create-the-workflow"></a>Erstellen des Workflows

1. Falls noch nicht vorhanden, erstellen Sie eine auf einem einzelnen Mandanten basierende Logik-App sowie einen leeren Workflow.

1. Nachdem der Designer geöffnet wurde, fügen Sie den Anforderungstrigger als ersten Schritt in Ihrem Workflow hinzu.

   > [!NOTE]
   > Sie können Anforderungstrigger und Webhooktrigger nur innerhalb Ihres virtuellen Netzwerks aufrufen. Webhooktrigger und -aktionen für verwaltete APIs funktionieren nicht, da sie einen öffentlichen Endpunkt zum Empfangen von Aufrufen erfordern. 

1. Fügen Sie je nach Ihren Szenarioanforderungen weitere Aktionen hinzu, die Sie in Ihrem Workflow ausführen möchten.

1. Wenn Sie fertig sind, speichern Sie Ihren Workflow.

Weitere Informationen finden Sie unter [Erstellen von Logik-App-Workflows mit nur einem Mandanten in Azure Logic Apps](create-single-tenant-workflows-azure-portal.md).

#### <a name="copy-the-endpoint-url"></a>Kopieren der Endpunkt-URL

1. Wählen Sie im Menü „Workflow“ die Option **Übersicht** aus.

1. Kopieren Sie von der Seite **Übersicht** die **Workflow-URL**, und speichern Sie sie zur späteren Verwendung.

   Um den Workflow auszulösen, rufen Sie diese URL auf, oder senden Sie eine Anforderung an diese.

1. Stellen Sie sicher, dass die URL funktioniert, indem Sie sie aufrufen oder eine Anforderung an sie senden. Sie können ein beliebiges Tool zum Senden der Anforderung verwenden, z. B. Postman.

### <a name="set-up-private-endpoint-connection"></a>Einrichten einer Verbindung mit dem privaten Endpunkt

1. Wählen Sie in Ihrem Logik-App-Menü unter **Einstellungen** die Option **Netzwerk** aus.

1. Wählen Sie auf der Seite **Netzwerk** unter **Verbindungen mit privaten Endpunkten** die Option **Verbindungen mit privaten Endpunkten konfigurieren** aus.

1. Wählen Sie im auf der Seite **Verbindungen mit privaten Endpunkten** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Privaten Endpunkt hinzufügen**, der geöffnet wird, die angeforderten Informationen zum Endpunkt an.

   Weitere Informationen finden Sie unter [Eigenschaften eines privaten Endpunkts](../private-link/private-endpoint-overview.md#private-endpoint-properties).

1. Nachdem Azure den privaten Endpunkt erfolgreich bereitgestellt hat, versuchen Sie erneut, die Workflow-URL aufzurufen.

   Diesmal erhalten Sie einen erwarteten `403 Forbidden`-Fehler, was bedeutet, dass der private Endpunkt eingerichtet ist und ordnungsgemäß funktioniert.

1. Um sicherzustellen, dass die Verbindung ordnungsgemäß funktioniert, erstellen Sie einen virtuellen Computer in demselben virtuellen Netzwerk mit dem privaten Endpunkt, und versuchen Sie, den Logik-App-Workflow aufzurufen.

### <a name="considerations-for-inbound-traffic-through-private-endpoints"></a>Überlegungen zum eingehenden Datenverkehr über private Endpunkte

- Bei Zugriffen von außerhalb Ihres virtuellen Netzwerks kann die Überwachungsansicht nicht auf die Eingaben und Ausgaben von Triggern und Aktionen zugreifen.

- Die Bereitstellung über Visual Studio Code oder die Azure CLI funktioniert nur innerhalb des virtuellen Netzwerks. Sie können das Bereitstellungscenter verwenden, um Ihre Logik-App mit einem GitHub-Repository zu verknüpfen. Sie können dann die Azure-Infrastruktur verwenden, um Ihren Code zu erstellen und bereitzustellen.

  Damit die GitHub-Integration funktioniert, entfernen Sie die `WEBSITE_RUN_FROM_PACKAGE`-Einstellung aus Ihrer Logik-App, oder legen Sie den Wert auf `0` fest.

- Das Aktivieren von Private Link wirkt sich nicht auf ausgehenden Datenverkehr aus, der weiterhin die App Service-Infrastruktur durchläuft.

<a name="set-up-outbound"></a>

## <a name="set-up-outbound-traffic-through-private-endpoints"></a>Einrichten von ausgehendem Datenverkehr über private Endpunkte

Um ausgehenden Datenverkehr aus Ihrer Logik-App zu schützen, können Sie Ihre Logik-App in ein virtuelles Netzwerk integrieren. Standardmäßig wird ausgehender Datenverkehr aus Ihrer Logik-App nur von Netzwerksicherheitsgruppen und benutzerdefinierten Routen (User-Defined Routes, UDRs) beeinflusst, wenn er an eine private Adresse wie `10.0.0.0/8`, `172.16.0.0/12` oder `192.168.0.0/16` geleitet wird.

Wenn Sie Ihren eigenen Domänennamenserver (DNS) mit Ihrem virtuellen Netzwerk verwenden, legen Sie die App-Einstellung `WEBSITE_DNS_SERVER` Ihrer Logik-App-Ressource auf die IP-Adresse Ihres DNS fest. Wenn Sie über ein sekundäres DNS verfügen, fügen Sie eine weitere App-Einstellung mit dem Namen `WEBSITE_DNS_ALT_SERVER` hinzu, und legen Sie ihren Wert ebenfalls auf die IP-Adresse Ihres DNS fest. Aktualisieren Sie außerdem Ihre DNS-Einträge, um auf Ihre privaten Endpunkte unter Ihrer internen IP-Adresse zu verweisen. Bei privaten Endpunkten wird die DNS-Suche an die private Adresse und nicht an die öffentliche Adresse für die jeweilige Ressource gesendet. Weitere Informationen finden Sie unter [Private Endpunkte: Integrieren Ihrer App in ein Azure Virtual Network](../app-service/overview-vnet-integration.md#private-endpoints).

> [!IMPORTANT]
> Damit die Azure Logic Apps-Runtime funktioniert, benötigen Sie eine ununterbrochene Verbindung mit dem Back-End-Speicher. Damit von Azure gehostete verwaltete Connectors funktionieren, benötigen Sie eine ununterbrochene Verbindung mit dem verwalteten API-Dienst.

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>Überlegungen zum ausgehenden Datenverkehr über private Endpunkte

Das Einrichten der Integration virtueller Netzwerke wirkt sich nur auf ausgehenden Datenverkehr aus. Informationen zum Schützen des eingehenden Datenverkehrs, der weiterhin den freigegebenen App Service-Endpunkt verwendet, finden Sie unter [Einrichten von eingehendem Datenverkehr über private Endpunkte](#set-up-inbound).

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/overview-vnet-integration.md)

- [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)

- [Routing von Datenverkehr für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>Verbinden des Speicherkontos mit privaten Endpunkten

Sie können den Speicherkontozugriff einschränken, sodass nur Ressourcen in einem virtuellen Netzwerk eine Verbindung herstellen können. Azure Storage unterstützt das Hinzufügen privater Endpunkte zu Ihrem Speicherkonto. Ihre Logik-App-Workflows können diese Endpunkte dann für die Kommunikation mit dem Speicherkonto verwenden. Weitere Informationen finden Sie unter [Verwenden privater Endpunkte für Azure Storage](../storage/common/storage-private-endpoints.md).

> [!NOTE]
> Für die folgenden Schritte muss vorübergehend der öffentliche Zugriff auf Ihr Speicherkonto aktiviert werden. Wenn Sie den öffentlichen Zugriff aufgrund der Richtlinien Ihrer Organisation nicht aktivieren können, können Sie Ihre Logik-App trotzdem mit einem privaten Speicherkonto bereitstellen. Sie müssen dann allerdings eine Azure Resource Manager-Vorlage (ARM-Vorlage) für die Bereitstellung verwenden. Eine ARM-Beispielvorlage finden Sie unter [Deploy logic app using secured storage account with private endpoints](https://github.com/VeeraMS/LogicApp-deployment-with-Secure-Storage) (Bereitstellen von Logik-Apps mit einem geschützten Speicherkonto und privaten Endpunkten).

1. Erstellen Sie für jeden Tabellen-, Warteschlangen-, Blob- und Dateispeicherdienst unterschiedliche private Endpunkte.

1. Aktivieren Sie den temporären öffentlichen Zugriff auf Ihr Speicherkonto, wenn Sie Ihre Logik-App bereitstellen.

   1. Öffnen Sie Ihre Speicherkontoressource im [Azure-Portal](https://portal.azure.com).

   1. Wählen Sie im Menü der Speicherkontoressource unter **Sicherheit + Netzwerkbetrieb** die Option **Netzwerk** aus.

   1. Wählen Sie im Bereich **Netzwerk** auf der Registerkarte **Firewalls und virtuelle Netzwerke** unter **Zugriff zulassen von** die Option **Alle Netzwerke** aus.

1. Stellen Sie Ihre Logik-App-Ressource über das Azure-Portal oder in Visual Studio Code bereit.

1. Aktivieren Sie nach Abschluss der Bereitstellung die Integration zwischen Ihrer Logik-App und den privaten Endpunkten in dem virtuellen Netzwerk oder Subnetz, das mit Ihrem Speicherkonto verbunden ist.

   1. Öffnen Sie Ihre Logik-App-Ressource im [Azure-Portal](https://portal.azure.com).

   1. Wählen Sie im Menü Ihrer Logik-App-Ressource unter **Einstellungen** die Option **Netzwerk** aus.

   1. Richten Sie die erforderlichen Verbindungen zwischen Ihrer Logik-App und den IP-Adressen für die privaten Endpunkte ein.

   1. Legen Sie für den Zugriff auf die Daten Ihres Logik-App-Workflows über das virtuelle Netzwerk in den Ressourceneinstellungen Ihrer Logik-App die Einstellung `WEBSITE_CONTENTOVERVNET` auf `1` fest.

   Wenn Sie Ihren eigenen Domänennamenserver (DNS) mit Ihrem virtuellen Netzwerk verwenden, legen Sie die App-Einstellung `WEBSITE_DNS_SERVER` Ihrer Logik-App-Ressource auf die IP-Adresse Ihres DNS fest. Wenn Sie über ein sekundäres DNS verfügen, fügen Sie eine weitere App-Einstellung mit dem Namen `WEBSITE_DNS_ALT_SERVER` hinzu, und legen Sie ihren Wert ebenfalls auf die IP-Adresse Ihres DNS fest. Aktualisieren Sie außerdem Ihre DNS-Einträge, um auf Ihre privaten Endpunkte unter Ihrer internen IP-Adresse zu verweisen. Bei privaten Endpunkten wird die DNS-Suche an die private Adresse und nicht an die öffentliche Adresse für die jeweilige Ressource gesendet. Weitere Informationen finden Sie unter [Private Endpunkte: Integrieren Ihrer App in ein Azure Virtual Network](../app-service/overview-vnet-integration.md#private-endpoints).

1. Nachdem Sie diese App-Einstellungen angewandt haben, können Sie den öffentlichen Zugriff von Ihrem Speicherkonto entfernen.

## <a name="next-steps"></a>Nächste Schritte

- [Logic Apps ohne Grenzen: Netzwerkoptionen mit Logic Apps (Einzelmandant)](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)