---
title: Lokales Datengateway für Azure Analysis Services | Microsoft-Dokumentation
description: Ein lokales Gateway ist erforderlich, wenn der Analysis Services-Server in Azure mit lokalen Datenquellen verbunden wird.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 302b61562e76c8e99f81febf936d8841f188e7ff
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136633"
---
# <a name="connecting-to-on-premises-data-sources-with-on-premises-data-gateway"></a>Herstellen einer Verbindung mit lokalen Datenquellen über ein lokales Datengateway

Das lokale Datengateway sorgt für eine sichere Datenübertragung zwischen lokalen Datenquellen und den Azure Analysis Services-Servern in der Cloud. Zusätzlich zur Verwendung von mehreren Azure Analysis Services-Servern in derselben Region funktioniert die neueste Version des Gateways auch mit Azure Logic Apps, Power BI, Power Apps und Power Automate. Auch wenn das installierte Gateway für alle diese Dienste identisch ist, sind für Azure Analysis Services und Logic Apps einige zusätzliche Schritte erforderlich.

Die hier bereitgestellten Informationen beziehen sich speziell auf die Funktionsweise von Azure Analysis Services mit dem lokalen Datengateway. Weitere Informationen zum Gateway im Allgemeinen und zu seiner Funktionsweise mit anderen Diensten finden Sie unter [Was ist ein lokales Datengateway?](/data-integration/gateway/service-gateway-onprem).

Für Azure Analysis Services umfasst das erstmalige Einrichten des Gateways vier Schritte:

- **Herunterladen und Ausführen des Setupprogramms:** Bei diesem Schritt wird ein Gatewaydienst auf einem Computer in Ihrer Organisation installiert. Sie melden sich bei Azure ebenfalls mit einem Konto in der Azure AD-Instanz Ihres [Mandanten](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) an. Azure B2B (Gast)-Konten werden nicht unterstützt.

- **Registrieren des Gateways**: In diesem Schritt geben Sie einen Namen und einen Wiederherstellungsschlüssel für Ihr Gateway ein, wählen eine Region aus und registrieren Ihr Gateway beim Gatewayclouddienst. Die Gatewayressource kann in jeder Region registriert werden, es empfiehlt sich aber, die gleiche Region zu verwenden, in der sich auch die Analysis Services-Server befinden. 

- **Erstellen einer Gatewayressource in Azure:** In diesem Schritt erstellen Sie eine Gatewayressource in Azure.

- **Verbinden der Gatewayressource mit Servern:** Nachdem Sie eine Gatewayressource eingerichtet haben, können Sie damit beginnen, Server mit dieser zu verbinden. Sie können mehrere Server und andere Ressourcen verbinden, sofern sie sich in derselben Region befinden.

## <a name="installing"></a>Installation

Wenn Sie die Installation für eine Azure Analysis Services-Umgebung durchführen, ist es wichtig, die unter [Installieren und Konfigurieren eines lokalen Datengateways](analysis-services-gateway-install.md) beschriebenen Schritte auszuführen. Die Informationen in diesem Artikel gelten speziell für Azure Analysis Services. Es werden zusätzliche Schritte genannt, die zum Einrichten einer lokalen Datengatewayressource in Azure sowie zur Verbindungsherstellung zwischen Ihrem Azure Analysis Services-Server und der Ressource erforderlich sind.

## <a name="connecting-to-a-gateway-resource-in-a-different-subscription"></a>Verbinden mit einer Gatewayressource in einem anderen Abonnement

Es wird empfohlen, dass Sie Ihre Azure-Gatewayressource in demselben Abonnement wie Ihren Server erstellen. Sie können Ihre Server jedoch so konfigurieren, dass sie eine Verbindung mit einer Gatewayressource in einem anderen Abonnement herstellen. Das Herstellen einer Verbindung mit einer Gatewayressource in einem anderen Abonnement wird nicht unterstützt, wenn Sie vorhandene Servereinstellungen konfigurieren oder einen neuen Server im Portal erstellen, es lässt sich aber mithilfe von PowerShell konfigurieren. Weitere Informationen finden Sie unter [Verbinden einer Gatewayressource mit einem Server](analysis-services-gateway-install.md#connect-gateway-resource-to-server).

## <a name="ports-and-communication-settings"></a>Ports und Kommunikationseinstellungen

Das Gateway stellt eine ausgehende Verbindung mit dem Azure Service Bus her. Es kommuniziert über ausgehende Ports: TCP 443 (Standard), 5671, 5672, 9350 bis 9354.  Das Gateway benötigt keine eingehenden Ports.

Möglicherweise müssen Sie die IP-Adressen für Ihre Datenregion für die Firewall einschließen. Sie können die [Liste der IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=56519) (in englischer Sprache) herunterladen. Diese Liste wird wöchentlich aktualisiert. Die IP-Adressen in der Liste der IP-Bereiche des Azure-Rechenzentrums sind in der CIDR-Notation angegeben. Weitere Informationen finden Sie unter [Klassenloses domänenübergreifendes Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Nachfolgend sind die vollqualifizierten Domänennamen aufgeführt, die vom Gateway verwendet werden.

| Domänennamen | Ausgehende Ports | BESCHREIBUNG |
| --- | --- | --- |
| *.powerbi.com |80 |Zum Herunterladen des Installers wird HTTP verwendet. |
| *.powerbi.com |443 |HTTPS |
| *. analysis.windows.net |443 |HTTPS |
| *.login.windows.net, login.live.com, aadcdn.msauth.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Advanced Message Queuing Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Listener auf Service Bus Relay per TCP (443 für Access Control-Tokenbeschaffung erforderlich) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Wird verwendet, um die Internetkonnektivität zu testen, falls der Power BI-Dienst das Gateway nicht erreicht. |
| *.microsoftonline-p.com |443 |Wird je nach Konfiguration für die Authentifizierung verwendet. |
| dc.services.visualstudio.com    |443 |Werden von AppInsights zur Erfassung von Telemetrie verwendet. |

## <a name="next-steps"></a>Nächste Schritte 

Die folgenden Artikel gehören zur allgemeinen Dokumentation für lokale Datengateways und gelten für alle Dienste, die das Gateway unterstützt:

* [Lokales Datengateway: Häufig gestellte Fragen](/data-integration/gateway/service-gateway-onprem-faq)   
* [Verwenden der App für das lokale Datengateway](/data-integration/gateway/service-gateway-app)   
* [Verwaltung auf Mandantenebene](/data-integration/gateway/service-gateway-tenant-level-admin)
* [Konfigurieren von Proxyeinstellungen](/data-integration/gateway/service-gateway-proxy)   
* [Anpassen von Kommunikationseinstellungen](/data-integration/gateway/service-gateway-communication)   
* [Konfigurieren von Protokolldateien](/data-integration/gateway/service-gateway-log-files)   
* [Problembehandlung](/data-integration/gateway/service-gateway-tshoot)
* [Überwachen und Optimieren der Gatewayleistung](/data-integration/gateway/service-gateway-performance)
