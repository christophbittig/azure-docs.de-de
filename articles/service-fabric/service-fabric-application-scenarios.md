---
title: Anwendungsszenarien und -entwurf
description: Übersicht über die Kategorien von Cloudanwendungen in Service Fabric. Informationen zum Anwendungsentwurf mit zustandsbehafteten und zustandslosen Diensten
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 703adde38d195395ae07dea022da873754e1d2f1
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867138"
---
# <a name="service-fabric-application-scenarios"></a>Service Fabric-Anwendungsszenarien

Azure Service Fabric bietet eine zuverlässige und flexible Plattform, auf der Sie verschiedenste Geschäftsanwendungen und -dienste erstellen und ausführen können. Diese Anwendungen und Microservices können zustandslos oder zustandsbehaftet sein, und deren Ressourcen werden zur Maximierung der Effizienz auf die virtuellen Maschinen verteilt.

In der einzigartigen Architektur von Service Fabric können Sie Echtzeitdatenanalysen, In-Memory-Berechnungen, parallele Transaktionen und die Ereignisverarbeitung in Ihren Anwendungen ausführen. Sie können Ihre Anwendungen je nach den sich ändernden Ressourcenanforderungen problemlos skalieren.

Einen Entwurfsleitfaden für Anwendungen finden Sie unter [Microservices-Architektur in Azure Service Fabric](/azure/architecture/reference-architectures/microservices/service-fabric) und [Bewährte Methoden für den Azure Service Fabric-Anwendungsentwurf](service-fabric-best-practices-applications.md).

Ein Verwenden der Service Fabric-Plattform bietet sich für die folgenden Typen von Anwendungen an:

* **Datensammlung, -verarbeitung und IoT:** Die Service Fabric-Plattform verarbeitet große Datenmengen und weist aufgrund ihrer zustandsbehafteten Dienste eine geringe Latenz auf. Mit Service Fabric können Daten auf Millionen von Geräten verarbeitet werden, auf denen die Daten für das Gerät und die Berechnung zusammengestellt werden.

    Zu den Kunden, die IoT-Dienste durch Verwenden von Service Fabric erstellt haben, gehören [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric) und [Mesh Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Gaming- und sitzungsbasierte interaktive Anwendungen:** Service Fabric ist nützlich, wenn Ihre Anwendung eine geringe Latenz bei Lese- und Schreibvorgängen erfordert, zum Beispiel für Onlinegaming oder Chats. Mit Service Fabric können Sie diese interaktiven, zustandsbehafteten Anwendungen erstellen, ohne einen separaten Speicher oder Cache erstellen zu müssen. Lesen Sie [Gaminglösungen von Azure](https://azure.microsoft.com/solutions/gaming/). Dort finden Sie einen Entwurfsleitfaden zum [Verwenden von Service Fabric in Gamingdiensten](/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Zu den Kunden, die Gamingdienste mit erstellt haben, gehören [Next Games](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) und [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Zu den Kunden, die interaktive Sitzungen erstellt haben, gehören [Honeywell mit Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Datenanalysen und Workflowverarbeitung:** Anwendungen, die Ereignisse oder Datenströme zuverlässig verarbeiten müssen, profitieren von den optimierten Lese- und Schreibvorgängen in Service Fabric. Außerdem unterstützt Service Fabric Pipelines für die Anwendungsverarbeitung, bei denen Ergebnisse zuverlässig sein und ohne Datenverlust in die nächste Verarbeitungsphase weitergeleitet werden müssen. Zu diesen Pipelines gehören Transaktions- und Finanzsysteme, bei denen Datenkonsistenz und Berechnungsgewährleistung von größter Wichtigkeit sind.

    Zu den Kunden, die Geschäftsworkflows erstellt haben, gehören die [Zeiss Gruppe ](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric) und [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Berechnung von Daten:** Mit Service Fabric können Sie zustandsbehaftete Anwendungen erstellen, die intensive Datenberechnungen durchführen. Service Fabric ermöglicht die Zusammenstellung von Verarbeitung (Berechnung) und Daten in Anwendungen. 

   Wenn Ihre Anwendung auf Daten zugreifen muss, begrenzt die Netzwerklatenz, die auf die Verwendung eines externen Datencache oder einer externen Speicherebene zurückzuführen ist, in der Regel die Rechenzeit. Zustandsbehaftete Service Fabric-Dienste vermeiden diese Latenz, wodurch eine Optimierung von Lese- und Schreibvorgängen ermöglicht wird.

   Beispiel: Angenommen, Ihre Anwendung unterbreitet Kunden nahezu in Echtzeit Vorschläge. Bei den Anforderungen muss eine Roundtrip-Zeit von unter 100 Millisekunden erreicht werden. Im Vergleich zum standardmäßigen Implementierungsmodell, bei dem die benötigten Daten aus einem Remotespeicher abgerufen werden müssen, bieten die Latenz- und Leistungsmerkmale von Service Fabric-Diensten den Benutzern eine schneller reagierende Umgebung. Das System reagiert schneller, weil die Berechnung der Vorschlagsauswahl mit den Daten und Regeln zusammengestellt ist.

    Zu den Kunden, die Berechnungsdienste erstellt haben, gehören [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) und [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Hoch verfügbare Dienste:** Service Fabric ermöglicht ein schnelles Failover, indem mehrere sekundäre Dienstreplikate erstellt werden. Wenn ein Knoten, Prozess oder individueller Dienst aufgrund eines hardwarebedingten oder anderen Fehlers ausfällt, wird eines der sekundären Replikate zu einem primären Replikat weitergeleitet – mit minimaler Dienstunterbrechung.

* **Skalierbare Dienste**: Einzelne Dienste können partitioniert werden, sodass der Zustand im Cluster horizontal hochskaliert werden kann. Darüber hinaus können einzelne Dienste spontan erstellt und entfernt werden. Sie können Dienste von wenigen Instanzen auf wenigen Knoten auf Tausende Instanzen auf vielen Knoten erweitern (hochskalieren) und diese Dienste dann nach Bedarf wieder herunterskalieren. Mithilfe von Service Fabric können Sie diese Dienste erstellen und ihren gesamten Lebenszyklus verwalten.

## <a name="application-design-case-studies"></a>Fallstudien zum Anwendungsentwurf

Fallstudien, die zeigen, wie Service Fabric zum Entwerfen von Anwendungen verwendet wird, sind auf den Websites [Kundenstimmen](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) und [Microservices in Azure](https://azure.microsoft.com/solutions/microservice-applications/) veröffentlicht.

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Entwerfen von Anwendungen, die aus zustandslosen und zustandsbehafteten Microservices bestehen

Das Erstellen von Anwendungen mit Azure Cloud Services-Workerrollen ist ein Beispiel für einen zustandslosen Dienst. Im Gegensatz dazu behalten zustandsbehaftete Microservices den autorisierenden Zustand über die Anforderung und ihre Antwort hinaus bei. Durch diese Funktion lässt sich über einfache APIs, die für replikationsgestützte Transaktionsgarantien sorgen, Hochverfügbarkeit und Konsistenz erreichen.

Zustandsbehaftete Dienste in Service Fabric ermöglichen Hochverfügbarkeit für alle Typen von Anwendungen – nicht nur für Datenbanken und andere Datenspeicher. Hierbei handelt es sich um einen natürlichen Prozess: Anwendungen haben bereits den Wandel von der Verwendung rein relationaler Datenbanken für Hochverfügbarkeit zur Verwendung von NoSQL-Datenbanken vollzogen. Nun werden die Anwendung selbst hochverfügbar, und Daten werden zur Leistungsoptimierung innerhalb der Anwendung verwaltet, ohne dafür Kompromisse bei Zuverlässigkeit, Konsistenz und Verfügbarkeit einzugehen.

Wenn Sie Anwendungen erstellen, die aus Microservices bestehen, haben Sie üblicherweise eine Kombination aus zustandslosen Web-Apps (z.B. ASP.NET und Node.js), aus denen zustandslose und zustandsbehaftete Unternehmensdienste der mittleren Ebene aufgerufen werden. Die Apps und Dienste werden alle über die Service Fabric-Bereitstellungsbefehle im selben Service Fabric-Cluster bereitgestellt. Jeder dieser Dienste ist hinsichtlich Skalierung, Zuverlässigkeit und Ressourcennutzung unabhängig. Diese Unabhängigkeit verbessert die Flexibilität in der Entwicklung und in der Lebenszyklusverwaltung.

Zustandsbehaftete Microservices vereinfachen Anwendungsentwürfe, da durch sie die Notwendigkeit zusätzlicher Warteschlangen und Caches entfällt, die traditionell erforderlich waren, um die Verfügbarkeits- und Latenzanforderungen von rein zustandslosen Anwendungen zu erfüllen. Da zustandsbehaftete Dienste Hochverfügbarkeit und niedrige Latenz bieten, gibt es weniger Details, die in Ihrer Anwendung verwaltet werden müssen.

In den folgenden Abbildungen werden die Unterschiede veranschaulicht, die es zwischen dem Entwerfen einer zustandslosen und einer zustandsbehafteten Anwendung gibt. Zustandsbehaftete Dienste verringern durch Nutzung der Programmiermodelle [Reliable Services](service-fabric-reliable-services-introduction.md) und [Reliable Actors](service-fabric-reliable-actors-introduction.md) die Komplexität der Anwendung. Gleichzeitig wird eine hohe Durchsatzrate mit geringer Latenz erzielt.

Hier sehen Sie eine Beispielanwendung, die zustandslose Dienste verwendet: ![Anwendung mit zustandslosen Diensten][Image1]

Hier sehen Sie eine Beispielanwendung, die zustandsbehaftete Dienste verwendet: ![Anwendung mit zustandsbehafteten Diensten][Image2]

## <a name="next-steps"></a>Nächste Schritte

* Beginnen Sie mit der Erstellung zustandsloser und zustandsbehafteter Dienste mit den Service Fabric-Programmiermodellen [Reliable Services](service-fabric-reliable-services-quick-start.md) und [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Besuchen Sie das Azure Architecture Center. Dort finden Sie einen Leitfaden zum [Erstellen von Microservices in Azure](/azure/architecture/microservices/).
* Einen Entwurfsleitfaden für Anwendungen finden Sie unter [Bewährte Methoden für Azure Service Fabric-Anwendungen und -Cluster](./service-fabric-best-practices-security.md).

* Weitere Informationen:
  * [Grundlegendes zu Microservices](service-fabric-overview-microservices.md)
  * [Definieren und Verwalten des Dienstzustands](service-fabric-concepts-state.md)
  * [Availability of services (in englischer Sprache)](service-fabric-availability-services.md)
  * [Skalieren von Diensten](service-fabric-concepts-scalability.md)
  * [Partitionieren von Diensten](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
