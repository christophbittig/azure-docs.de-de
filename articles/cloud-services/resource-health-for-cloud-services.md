---
title: Resource Health für Cloud Services (Classic)
description: In diesem Artikel wird die Unterstützung der Ressourcenintegritätsprüfung (Resource Health Check, RHC) für Microsoft Azure Cloud Services (Classic) beschrieben.
ms.topic: article
ms.service: cloud-services
ms.subservice: resource-health
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 6ea4875ecfa70c6c3ff07e3dc31ef71b023b44ce
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823543"
---
# <a name="resource-health-check-rhc-support-for-azure-cloud-services-classic"></a>Unterstützung der Ressourcenintegritätsprüfung (RHC) für Azure Cloud Services (Classic)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

In diesem Artikel wird die Unterstützung der Ressourcenintegritätsprüfung (Resource Health Check, RHC) für [Microsoft Azure Cloud Services (Classic)](https://azure.microsoft.com/services/cloud-services) beschrieben.

Mithilfe von [Azure Resource Health](../service-health/resource-health-overview.md) für Cloud Services erhalten Sie bei Problemen mit der Bereitstellung, mit Rollen und Rolleninstanzen von Cloud Services Unterstützung und Hilfe bei der Diagnose. Sie werden über die aktuelle und frühere Integrität von Cloud Services auf der Bereitstellungs-, Rollen- und Rolleninstanzebene informiert.

Im Azure-Status werden Probleme gemeldet, die eine breite Palette von Azure-Kunden betreffen. Resource Health stellt die Integrität Ihrer Ressourcen in einem personalisierten Dashboard dar. Resource Health zeigt alle Zeitpunkte an, zu denen Ihre Ressourcen aufgrund von Azure-Dienstproblemen nicht verfügbar waren. Mit diesen Daten können Sie leicht feststellen, ob gegen eine SLA verstoßen wurde.

:::image type="content" source="media/cloud-services-allocation-failure/rhc-blade-cloud-services.png" alt-text="Abbildung des Blatts zur Ressourcenintegritätsprüfung im Azure-Portal":::

## <a name="how-health-is-checked-and-reported"></a>Wie wird die Integrität geprüft und gemeldet?
Berichte zur Ressourcenintegrität werden auf der Bereitstellungs- oder Rollenebene erstellt. Die Integritätsprüfung erfolgt auf der Ebene der Rolleninstanz. Anschließend wird der Status aggregiert und auf Rollenebene gemeldet. Beispiel: Sobald alle Rolleninstanzen verfügbar sind, steht der Rollenstatus zur Verfügung. Auf die gleiche Weise wird der Integritätsstatus aller Rollen aggregiert und auf der Bereitstellungsebene gemeldet. Beispiel: Sobald alle Rollen verfügbar sind, steht der Bereitstellungsstatus zur Verfügung. 

## <a name="why-i-cannot-see-health-status-for-my-staging-slot-deployment"></a>Warum wird der Integritätsstatus für Bereitstellungen im Stagingslot nicht angezeigt?
Eine Überprüfung der Ressourcenintegrität kann nur für Bereitstellungen im Produktionsslot ausgeführt werden. Stagingslot-Bereitstellungen werden derzeit noch nicht unterstützt. 

## <a name="does-resource-health-check-also-check-the-health-of-the-application"></a>Wird bei der Ressourcenintegritätsprüfung auch die Integrität der Anwendung überprüft?
Nein, die Integritätsprüfung wird nur für Rolleninstanzen durchgeführt. Die Anwendungsintegrität wird nicht überwacht. Beispiel: Ist eine von drei Rolleninstanzen fehlerhaft, kann die Anwendung weiterhin verfügbar sein. RHC verwendet keine [Load Balancer-Tests](../load-balancer/load-balancer-custom-probe-overview.md) oder Gast-Agent-Tests. Daher sollten Benutzer weiterhin mithilfe von Load Balancer-Tests die Integrität ihrer Anwendung überwachen. 

## <a name="what-are-the-annotations-for-cloud-services"></a>Worum handelt es sich bei den Anmerkungen für Cloud Services?
Die Anmerkungen stellen den Integritätsstatus der Bereitstellung oder der Rollen dar. Es sind unterschiedliche Anmerkungen verfügbar, etwa zum Integritätsstatus, den Gründen für eine Statusänderung usw. 

## <a name="what-does-it-mean-by-role-instance-being-unavailable"></a>Was bedeutet es, wenn eine Rolleninstanz „nicht verfügbar“ ist?
Dies bedeutet, dass die Rolleninstanz kein fehlerfreies Signal an die Plattform ausgibt. Überprüfen Sie den Status der Rolleninstanz, um eine ausführliche Erläuterung dazu zu erhalten, warum kein fehlerfreies Signal ausgegeben wird.

## <a name="what-does-it-mean-by-deployment-being-unknown"></a>Was bedeutet es, wenn die Bereitstellung „unbekannt“ ist?
Dies bedeutet, dass die aggregierte Integrität der Bereitstellung von Cloud Services nicht bestimmt werden kann. Dies deutet in der Regel darauf hin, dass für Cloud Services keine Produktionsbereitstellung erstellt wurde, die Bereitstellung neu erstellt wurde (und Azure daher erst mit dem Sammeln von Integritätsereignissen begonnen hat) oder die Plattform Probleme mit dem Sammeln von Integritätsereignissen für die Bereitstellung aufweist.

## <a name="why-does-role-instance-annotations-mentions-vms-instead-of-role-instances"></a>Warum ist in den Anmerkungen zur Rolleninstanz die Rede von „virtuellen Computern“ anstelle von „Rolleninstanzen“?
Im Grunde stellen Rolleninstanzen virtuelle Computer dar, und die Integritätsprüfung für virtuelle Computer wird auch für Rolleninstanzen verwendet. Daher wird der Begriff „virtuelle Computer“ für Rolleninstanzen verwendet. 

## <a name="cloud-services-deployment-level-annotations--their-meanings"></a>Bedeutung der Anmerkungen zu Cloud Services (Bereitstellungsebene)
| Anmerkung | BESCHREIBUNG | 
| --- | --- | 
| Verfügbar| Es sind keine Probleme der Azure-Plattform mit Auswirkungen auf diese Bereitstellung von Cloud Services bekannt. |
| Unbekannt | Die Integrität dieser Bereitstellung von Cloud Services kann zurzeit nicht ermittelt werden. | 
| Resource Health wird eingerichtet | Resource Health wird für diese Ressource eingerichtet. Ihre Azure-Ressourcen werden von Resource Health überwacht, um detaillierte Informationen zu aktuellen und früheren Ereignissen mit Auswirkungen auf deren Integrität bereitzustellen.|
| Heruntergestuft | Ihre Clouddienstbereitstellung ist beeinträchtigt. Wir arbeiten daran, Ihre Clouddienstbereitstellung automatisch wiederherzustellen und die Problemursache zu ermitteln. Derzeit sind keine weiteren Schritte erforderlich. |
| Unhealthy | Ihre Bereitstellung von Cloud Services ist fehlerhaft, weil {0} von {1} Rolleninstanzen nicht verfügbar sind. |
| Heruntergestuft | Ihre Bereitstellung von Cloud Services ist beeinträchtigt, weil {0} von {1} Rolleninstanzen nicht verfügbar sind. | 
| Verfügbar und möglicherweise beeinträchtigt | Ihre Bereitstellung von Cloud Services wird ausgeführt, doch der aktuelle Ausfall eines Azure-Diensts verhindert möglicherweise, dass Sie eine Verbindung dazu herstellen können. Sobald der Ausfall behoben ist, wird die Verbindung wiederhergestellt. |
| Nicht verfügbar und möglicherweise beeinträchtigt | Die Integrität dieser Bereitstellung von Cloud Services ist möglicherweise durch den Ausfall eines Azure-Diensts beeinträchtigt. Ihre Bereitstellung von Cloud Services wird automatisch wiederhergestellt, sobald der Ausfall behoben ist. |
| Unbekannt und möglicherweise beeinträchtigt | Die Integrität dieser Bereitstellung von Cloud Services kann zurzeit nicht ermittelt werden. Dies kann an einem aktuellen Ausfall eines Azure-Diensts liegen, der sich auf diesen virtuellen Computer auswirkt. Sobald der Ausfall behoben ist, wird der virtuelle Computer automatisch wiederhergestellt. |

## <a name="cloud-services-role-instance-level-annotations--their-meanings"></a>Bedeutung der Anmerkungen zu Cloud Services (Rolleninstanzebene)
| Anmerkung | BESCHREIBUNG | 
| --- | --- | 
| Verfügbar | Es sind keine Probleme der Azure-Plattform mit Auswirkungen auf diesen virtuellen Computer bekannt. | 
| Unbekannt | Die Integrität dieses virtuellen Computers kann zurzeit nicht ermittelt werden. |
| Wurde beendet und wird neu zugeordnet | Dieser virtuelle Computer wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess beendet, und seine Zuordnung wird aufgehoben |
| Resource Health wird eingerichtet | Resource Health wird für diese Ressource eingerichtet. Ihre Azure-Ressourcen werden von Resource Health überwacht, um detaillierte Informationen zu aktuellen und früheren Ereignissen mit Auswirkungen auf deren Integrität bereitzustellen. |
| Nicht verfügbar | Ihr virtueller Computer ist nicht verfügbar. Wir arbeiten daran, Ihren virtuellen Computer automatisch wiederherzustellen und die Problemursache zu ermitteln. Derzeit sind keine weiteren Schritte erforderlich. |
| Heruntergestuft | Ihr virtueller Computer ist beeinträchtigt. Wir arbeiten daran, Ihren virtuellen Computer automatisch wiederherzustellen und die Problemursache zu ermitteln. Derzeit sind keine weiteren Schritte erforderlich. |
| Hardwarefehler des Hostservers | Dieser virtuelle Computer ist von einem schwerwiegenden Fehler vom Typ „{HardwareCategory}“ auf dem Hostserver betroffen. Azure stellt Ihren virtuellen Computer auf einem fehlerfreien Hostserver erneut bereit. |
| Migration aufgrund von beeinträchtigter Hardware geplant | Azure hat ermittelt, dass {0} auf dem Hostserver beeinträchtigt ist und ggf. in Kürze ausfällt. Falls dies machbar ist, führen wir für Ihren virtuellen Computer schnellstmöglich eine Livemigration bzw. nach {1} (UTC) auf andere Weise eine erneute Bereitstellung durch. Es wird empfohlen, den virtuellen Computer so bald wie möglich selbst erneut bereitzustellen, falls die Hardware vor der vom System initiierten Migration ausfällt. So wird das Risiko für Ihren Dienst minimiert. |
| Verfügbar und möglicherweise beeinträchtigt | Ihr virtueller Computer wird ausgeführt, doch der aktuelle Ausfall eines Azure-Diensts verhindert möglicherweise, dass Sie eine Verbindung dazu herstellen können. Sobald der Ausfall behoben ist, wird die Verbindung wiederhergestellt. |
| Nicht verfügbar und möglicherweise beeinträchtigt | Die Integrität dieses virtuellen Computers ist möglicherweise durch den Ausfall eines Azure-Diensts beeinträchtigt. Ihr virtueller Computer wird automatisch wiederhergestellt, sobald der Ausfall behoben ist. |
| Unbekannt und möglicherweise beeinträchtigt | Die Integrität dieses virtuellen Computers kann zurzeit nicht ermittelt werden. Dies kann an einem aktuellen Ausfall eines Azure-Diensts liegen, der sich auf diesen virtuellen Computer auswirkt. Sobald der Ausfall behoben ist, wird der virtuelle Computer automatisch wiederhergestellt. |
| Zugeordnete Hardwareressourcen | Dem virtuellen Computer wurden Hardwareressourcen zugewiesen, und er wird in Kürze online geschaltet |
| Wird beendet und neu zugeordnet | Dieser virtuelle Computer wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess beendet, und seine Zuordnung wird aufgehoben |
| Konfiguration wird aktualisiert | Die Konfiguration dieses virtuellen Computers wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess aktualisiert |
| Vom Benutzer neu gestartet | Dieser virtuelle Computer wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess neu gestartet. Nachdem der Neustart abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Erneute Bereitstellung auf anderem Host | Dieser virtuelle Computer wird aufgrund einer Anforderung eines autorisierten Benutzers oder Prozesses auf einem anderen Host erneut bereitgestellt. Nachdem die erneute Bereitstellung abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Vom Benutzer beendet | Dieser virtuelle Computer wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess beendet |
| Vom Benutzer oder Prozess beendet | Dieser virtuelle Computer wurde angehalten, weil ein autorisierter Benutzer dies angefordert hat oder ein bestimmter Prozess auf dem virtuellen Computer ausgeführt wurde |
| Vom Benutzer gestartet | Dieser virtuelle Computer wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess gestartet. Er wird in Kürze wieder in den Onlinezustand versetzt. |
| Erneute Wartungsbereitstellung auf anderem Host | Dieser virtuelle Computer wird im Rahmen einer geplanten Wartungsaktivität auf einem anderen Hostserver erneut bereitgestellt. Nachdem die erneute Bereitstellung abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Neustart von innerhalb des Computers initiiert | Auf dem virtuellen Computer wurde ein Neustart ausgelöst. Der Grund kann ein Betriebssystemfehler auf dem virtuellen Computer oder eine Anforderung durch einen autorisierten Benutzer oder Prozess sein. Nachdem der Neustart abgeschlossen ist, wird der virtuelle Computer wieder in den Onlinezustand versetzt. |
| Größenänderung durch Benutzer | Die Größe dieses virtuellen Computers wird gemäß der Anforderung durch einen autorisierten Benutzer oder Prozess geändert. Nachdem die Größenänderung abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Computer abgestürzt | Auf dem virtuellen Computer wurde ein Neustart ausgelöst. Der Grund kann ein Betriebssystemfehler auf dem virtuellen Computer oder eine Anforderung durch einen autorisierten Benutzer oder Prozess sein. Nachdem der Neustart abgeschlossen ist, wird der virtuelle Computer wieder in den Onlinezustand versetzt. |
| Wartungsneustart für Hostupdate | Wartungsupdates werden auf den Hostserver angewendet, auf dem dieser virtuelle Computer ausgeführt wird. Derzeit müssen Sie keine weiteren Schritte ausführen. Nachdem die Wartung abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Erneute Wartungsbereitstellung auf neuer Hardware | Dieser virtuelle Computer ist nicht verfügbar, weil er im Rahmen eines geplanten Wartungsereignisses auf neuerer Hardware erneut bereitgestellt wird. Derzeit müssen Sie keine weiteren Schritte ausführen. Nachdem die geplante Wartung abgeschlossen ist, wird er wieder in den Onlinezustand versetzt. |
| Computer mit niedriger Priorität vorzeitig entfernt | Dieser virtuelle Computer wurde vorzeitig entfernt. Dieser virtuelle Computer mit niedriger Priorität wird beendet und neu zugeordnet. |
| Neustart des Hostservers | Ihr virtueller Computer ist aufgrund eines unerwarteten Neustarts des Hostservers nicht verfügbar. Der Hostserver wird gerade neu gestartet. Nachdem der Neustart abgeschlossen ist, wird der virtuelle Computer wieder in den Onlinezustand versetzt. Derzeit sind keine weiteren Schritte erforderlich. |
| Erneute Bereitstellung aufgrund eines Hostfehlers | Ihr virtueller Computer ist leider nicht verfügbar und wird aufgrund eines unerwarteten Fehlers auf dem Hostserver erneut bereitgestellt. Azure hat den Prozess für die automatische Wiederherstellung gestartet und startet derzeit den virtuellen Computer auf einem anderen Host. Derzeit sind keine weiteren Schritte erforderlich. |
| Unerwarteter Hostfehler | Ihr virtueller Computer ist aufgrund eines unerwarteten Fehlers auf dem Hostserver nicht verfügbar. Azure hat den Prozess für die automatische Wiederherstellung gestartet und führt derzeit den Neustart des Hostservers durch. Derzeit müssen Sie keine weiteren Schritte ausführen. Nachdem der Neustart abgeschlossen ist, wird der virtuelle Computer wieder in den Onlinezustand versetzt. |
| Erneute Bereitstellung aufgrund von ungeplanter Hostwartung | Ihr virtueller Computer ist leider nicht verfügbar und wird aufgrund eines unerwarteten Fehlers auf dem Hostserver erneut bereitgestellt. Azure hat den Prozess für die automatische Wiederherstellung gestartet und startet derzeit den virtuellen Computer auf einem anderen Hostserver. Derzeit sind keine weiteren Schritte erforderlich. |
| Bereitstellungsfehler | Ihr virtueller Computer ist aufgrund von unerwarteten Bereitstellungsproblemen nicht verfügbar. Bei der Bereitstellung Ihres virtuellen Computers ist ein unerwarteter Fehler aufgetreten. |
| Livemigration | Dieser virtuelle Computer wurde angehalten, weil eine speichererhaltende Livemigration durchgeführt wird. Die Ausführung des virtuellen Computers wird normalerweise innerhalb von zehn Sekunden fortgesetzt. Derzeit sind keine weiteren Schritte erforderlich. |
| Livemigration | Dieser virtuelle Computer wurde angehalten, weil eine speichererhaltende Livemigration durchgeführt wird. Die Ausführung des virtuellen Computers wird normalerweise innerhalb von zehn Sekunden fortgesetzt. Derzeit sind keine weiteren Schritte erforderlich. | 
| Remotedatenträger getrennt | Leider ist Ihr virtueller Computer aufgrund eines Konnektivitätsverlusts mit dem Remotedatenträger nicht verfügbar. Wir arbeiten daran, die Datenträgerkonnektivität wiederherzustellen. Derzeit sind keine weiteren Schritte erforderlich. |
| Azure-Dienstproblem | Ihr virtueller Computer ist von einem Problem mit dem Azure-Dienst betroffen. |
| Netzwerkproblem | Dieser virtuelle Computer wird durch ein Top-of-Rack-Netzwerkgerät beeinträchtigt. |
| Nicht verfügbar | Ihr virtueller Computer ist nicht verfügbar. Die Ursache für diesen Ausfall kann zurzeit nicht ermittelt werden. |
| Neustart des Hostservers | Ihr virtueller Computer ist aufgrund eines unerwarteten Neustarts des Hostservers nicht verfügbar. Ein unerwartetes Problem mit dem Hostserver verhindert, dass Ihr virtueller Computer automatisch wiederhergestellt wird. |
| Erneute Bereitstellung aufgrund eines Hostfehlers | Ihr virtueller Computer ist aufgrund eines unerwarteten Fehlers auf dem Hostserver nicht verfügbar. Ein unerwartetes Problem mit dem Host verhindert, dass Ihr virtueller Computer automatisch wiederhergestellt wird. |
| Unerwarteter Hostfehler | Ihr virtueller Computer ist aufgrund eines unerwarteten Fehlers auf dem Hostserver nicht verfügbar. Ein unerwartetes Problem mit dem Host verhindert, dass Ihr virtueller Computer automatisch wiederhergestellt wird. |
| Erneute Bereitstellung aufgrund von ungeplanter Hostwartung | Ihr virtueller Computer ist aufgrund eines unerwarteten Fehlers auf dem Hostserver nicht verfügbar. Ein unerwartetes Problem mit dem Host verhindert, dass Ihr virtueller Computer automatisch wiederhergestellt wird. |
| Bereitstellungsfehler | Ihr virtueller Computer ist aufgrund von unerwarteten Bereitstellungsproblemen nicht verfügbar. Bei der Bereitstellung Ihres virtuellen Computers ist ein unerwarteter Fehler aufgetreten. |
| Remotedatenträger getrennt | Leider ist Ihr virtueller Computer aufgrund eines Konnektivitätsverlusts mit dem Remotedatenträger nicht verfügbar. Ein unerwartetes Problem verhindert, dass Ihr virtueller Computer automatisch wiederhergestellt wird. |
| Neustart aufgrund eines Updates des Gastbetriebssystems | Wegen eines neuen Updates des Gastbetriebssystems wurde von der Azure-Plattform ein Neustart initiiert. Nachdem der Neustart abgeschlossen ist, wird der virtuelle Computer wieder in den Onlinezustand versetzt. |