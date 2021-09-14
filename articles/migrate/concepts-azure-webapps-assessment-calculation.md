---
title: Azure App Service-Bewertungen in Azure Migrate Discovery und Bewertungstool
description: Im Folgenden erhalten Sie Informationen zu den Azure App Service-Bewertungen in Azure Migrate Discovery und zum Bewertungstool.
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: ec83b7a8de449a73dc227941f864ac7cd8495e68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355144"
---
# <a name="assessment-overview-migrate-to-azure-app-service"></a>Übersicht der Bewertungen (Migration zu Azure App Service)

Dieser Artikel bietet eine Übersicht über die Bewertungen für die Migration lokaler ASP.NET-Web-Apps aus einer VMware-Umgebung zu Azure App Service mithilfe des [Azure Migrate-Tools zur Ermittlung und Bewertung](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Was ist eine Bewertung?
Eine Bewertung mit dem Ermittlungs- und Bewertungstool ermöglicht eine Momentaufnahme der Daten und misst die Bereitschaft lokaler Server zur Migration zu Azure. Außerdem liefert sie Details zu den Kosten für das Hosting von lokalen Servern, Datenbanken und Web-Apps in Azure.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Sie können vier Arten von Bewertungen mit dem Azure Migrate-Tool zur Ermittlung und Bewertung erstellen.

**Bewertungstyp** | **Details**
--- | ---
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen Server in der [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure-VMs mit diesem Bewertungstyp bewerten.
**Azure SQL** | Bewertungen zum Migrieren Ihrer lokalen SQL Server-Instanzen aus Ihrer VMware-Umgebung zu Azure SQL-Datenbank oder Azure SQL Managed Instance.
**Azure App Service** | Bewertungen zur Migration Ihrer lokalen, auf einem IIS-Webserver ausgeführten ASP.NET-Web-Apps, von Ihrer VMware-Umgebung zu Azure App Service
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

Eine Azure App Service-Bewertung bietet ein Kriterium für die Dimensionierung:

**Größenkriterien** | **Details** | **Daten**
--- | --- | ---
**Konfigurationsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Konfigurationsdaten liefern | Bei der Azure App Service-Bewertung werden nur Konfigurationsdaten für die Bewertungsberechnung berücksichtigt. Leistungsdaten für Web-Apps werden nicht erfasst.

## <a name="how-do-i-assess-my-on-premises-aspnet-web-apps"></a>Wie bewerte ich meine lokalen ASP.NET-Web-Apps?

Sie können anhand der Konfigurationsdaten, die von einer einfachen Azure Migrate-Appliance gesammelt werden, Ihre lokalen Web-Apps bewerten. Die Appliance ermittelt lokale Web-Apps und sendet Konfigurationsdaten an Azure Migrate. [Weitere Informationen](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?

Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:

1. Richten Sie Azure und Ihre lokale Umgebung so ein, dass Azure Migrate verwendet werden kann.
2. Erstellen Sie bei der ersten Bewertung ein Azure Migrate-Projekt. Das Azure Migrate-Tool zur Ermittlung und Bewertung wird dem Projekt standardmäßig hinzugefügt.
3. Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Konfigurations- und Leistungsdaten an Azure Migrate. Stellen Sie die Appliance als VM oder physischen Server bereit. Auf den Servern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Ermittlung begonnen hat, können Sie die (die Web-Apps hostenden) Server, die Sie bewerten möchten, einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure App Service** durchführen.

Folgen Sie den Anweisungen im Tutorial zum Bewerten von [ASP.NET-Web-Apps](tutorial-assess-webapps.md), um diese Schritte auszuprobieren.

## <a name="what-properties-are-used-to-customize-the-assessment"></a>Welche Eigenschaften werden zum Anpassen der Bewertung verwendet?

Die folgenden Eigenschaften sind in den Azure App Service-Bewertungseigenschaften enthalten:

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Die Azure-Region, zu der die Migration durchgeführt werden soll. Empfehlungen zur Azure App Service-Konfiguration und zu den Kosten basieren auf dem von Ihnen angegebenen Standort.
**Isolation erforderlich** | Wählen Sie „Ja“ aus, wenn Ihre Web-Apps in einer privaten und dedizierten Umgebung in einem Azure-Rechenzentrum mit VMs der Dv2-Serie mit schnelleren Prozessoren, SSD-Speicher und doppelt so großem Arbeitsspeicher/Kernverhältnis im Vergleich zu Standardplänen ausgeführt werden sollen.
**Reservierte Instanzen** | Geben Sie reservierte Instanzen an, damit diese bei Kostenschätzungen in der Bewertung berücksichtigt werden.<br/><br/> Wenn Sie eine Option für eine reservierte Instanz auswählen, können Sie nicht „Rabatt (%)“ angeben.
**Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. Bei der Bewertung werden die Kosten für dieses Angebot geschätzt.
**Währung** | Die Rechnungswährung für Ihr Konto.
**Rabatt (%)** | Abonnementspezifische Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
**EA-Abonnement** | Mit dieser Eigenschaft wird festgelegt, dass ein EA-Abonnement (Enterprise Agreement) für die Kostenschätzung verwendet wird. Dabei wird der Rabatt berücksichtigt, der für das Abonnement gilt. <br/><br/> Behalten Sie die Standardeinstellungen für die Eigenschaften „reservierte Instanzen“, „Rabatt (%)“ und „VM-Betriebszeit“ bei.

Sehen Sie sich die [bewährten Methoden](best-practices-assessment.md) für die Erstellung einer Bewertung mit Azure Migrate an.

## <a name="calculate-readiness"></a>Berechnen der Bereitschaft

### <a name="azure-app-service-readiness"></a>Azure App Service-Bereitschaft

Die Azure App Service-Bereitschaft für Web-Apps basiert auf Überprüfungen der Featurekompatibilität zwischen der lokalen Konfiguration von Web-Apps und Azure App Service:

1. Die Azure App Service-Bewertung berücksichtigt die Konfigurationsdaten der Web-Apps, um Kompatibilitätsprobleme zu identifizieren.
1. Wenn keine Kompatibilitätsprobleme gefunden werden, wird die Bereitschaft für den Zielbereitstellungstyp als **Bereit** gekennzeichnet.
1. Wenn unkritische Kompatibilitätsprobleme vorhanden sind, z. B. heruntergestufte oder nicht unterstützte Features, die die Migration zu einem bestimmten Zielbereitstellungstyp nicht blockieren, wird die Bereitschaft als **Bereit mit Bedingungen** mit einem Hyperlink zu **Warnungsdetails** und Anleitungen zur empfohlenen Korrektur gekennzeichnet.
1. Wenn Kompatibilitätsprobleme vorliegen, die die Migration zu einem bestimmten Zielbereitstellungstyp blockieren können, erfolgt die Kennzeichnung **Nicht bereit** mit **Problemdetails** und Anleitung zur empfohlenen Korrektur.
1. Wenn die Ermittlung noch ausgeführt wird oder Ermittlungsprobleme bei einer Web-App aufgetreten sind, wird die Bereitschaft als **Unbekannt** gekennzeichnet, da die Bewertung die Bereitschaft für diese Web-App nicht berechnen konnte.

## <a name="calculate-sizing"></a>Berechnen der Dimensionierung

### <a name="azure-app-service-sku"></a>Azure App Service-SKU

Nachdem die Bewertung die Bereitschaft anhand von Konfigurationsdaten ermittelt hat, wird die Azure App Service-SKU bestimmt, die für die Ausführung Ihrer Apps in Azure App Service geeignet ist.
Die Pläne vom Typ „Premium“ eignen sich für Produktionsworkloads und werden auf dedizierten virtuellen Computerinstanzen ausgeführt. Jede Instanz kann mehrere Anwendungen und Domänen unterstützen. Beim Plan vom Typ „Isoliert“ werden Ihre Apps in einer privaten, dedizierten Azure-Umgebung gehostet. Er eignet sich ideal für Apps, die eine sichere Verbindung mit dem lokalen Netzwerk erfordern.

> [!NOTE]
> Derzeit werden für Azure Migrate nur I1-, P1v2- und P1v3-SKUs empfohlen. In Azure App Service sind weitere SKUs verfügbar. [Weitere Informationen](https://azure.microsoft.com/pricing/details/app-service/windows/)

### <a name="azure-app-service-plan"></a>Azure App Service-Plan

In App Service wird eine App immer in einem [App Service-Plan](/azure/app-service/overview-hosting-plans) ausgeführt. Mit einem App Service-Plan wird ein Satz mit Computeressourcen für eine auszuführende Web-App definiert. Der Plan/die SKU wird im Allgemeinen gemäß der folgenden Tabelle bestimmt.

**Isolation erforderlich** | **Reservierte Instanz** | **App Service-Plan/-SKU**
--- | --- | ---
Ja  | Ja | I1
Ja  | Nein  | I1
Nein  | Ja | P1v3
Nein  | Nein | P1v2

### <a name="azure-app-service-cost-details"></a>Details zu Azure App Service-Kosten

Bei einem [App Service-Plan](/azure/app-service/overview-hosting-plans) fällt eine [Gebühr](https://azure.microsoft.com/pricing/details/app-service/windows/) für die genutzten Computeressourcen an. In App Service zahlen Sie Gebühren pro App Service-Plan und nicht pro Web-App. Es können eine oder mehrere Apps für die Ausführung auf denselben Computeressourcen (oder in demselben App Service-Plan) konfiguriert werden. Alle Apps, die Sie in diesen App Service-Plan einfügen, werden auf diesen Computeressourcen ausgeführt, wie in Ihrem App Service-Plan definiert.
Bei der Azure Migrate-Bewertung werden jedem empfohlenen App Service-Plan mehrere Web-Apps zugeordnet, um die Kosten zu optimieren. Die Anzahl der Web-Apps, die den einzelnen Planinstanzen zugeordnet werden, sind in der folgenden Tabelle aufgeführt.

**App Service-Plan** | **Web-Apps pro App Service-Plan**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

> [!NOTE]
> Ihr App Service-Plan kann jederzeit zentral hoch- und herunterskaliert werden. [Weitere Informationen](/azure/app-service/overview-hosting-plans#what-if-my-app-needs-more-capabilities-or-features/)

## <a name="next-steps"></a>Nächste Schritte
- [Bewährte Methoden für die Erstellung von Bewertungen](best-practices-assessment.md) 
- Weitere Informationen zum Ausführen einer [Azure App Service-Bewertung](how-to-create-azure-app-service-assessment.md)