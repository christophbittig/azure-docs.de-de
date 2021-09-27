---
title: Autorisieren des Zugriffs mit einer Shared Access Signature in Azure Event Hubs
description: Dieser Artikel enthält Informationen zur Autorisierung des Zugriffs auf Azure Event Hubs-Ressourcen mit Shared Access Signatures (SAS).
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: cc5ed89f9197bfb79735e558b9f36e22f87eba4b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561042"
---
# <a name="authorizing-access-to-event-hubs-resources-using-shared-access-signatures"></a>Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Shared Access Signatures
Mit einer Shared Access Signature (SAS) können Sie eingeschränkten Zugriff auf Ressourcen in Ihrem Event Hubs-Namespace gewähren. SAS schützt den Zugriff auf Event Hubs-Ressourcen mit Autorisierungsregeln. Diese Regeln werden entweder in einem Namespace oder in einer Entität (Event Hub oder Thema) konfiguriert. Dieser Artikel enthält eine Übersicht über das SAS-Modell und eine Beschreibung der bewährten Methoden für SAS.

## <a name="what-are-shared-access-signatures"></a>Was sind Shared Access Signatures?
Eine Shared Access Signature (SAS) ermöglicht basierend auf Autorisierungsregeln den delegierten Zugriff auf Event Hubs-Ressourcen. Eine Autorisierungsregel hat einen Namen, ist mit bestimmten Rechten verknüpft und enthält ein Paar kryptografischer Schlüssel. Sie verwenden den Namen und den Schlüssel der Regel über die Event Hubs-Clients oder in Ihrem eigenen Code, um SAS-Token zu generieren. Ein Client kann das Token dann an Event Hubs übergeben, um die Autorisierung für den angeforderten Vorgang zu bestätigen.

SAS ist ein anspruchsbasierter Autorisierungsmechanismus, bei dem einfache Token eingesetzt werden. Mit SAS werden Schlüssel nie über das Netzwerk übergeben. Schlüssel werden verwendet, um Informationen kryptografisch zu signieren, die später vom Dienst überprüft werden können. SAS kann wie ein Schema aus Benutzername und Kennwort verwendet werden, bei dem der Client unmittelbar den Namen einer Autorisierungsregel und einen passenden Schlüssel besitzt. SAS kann ähnlich wie ein Verbundsicherheitsmodell verwendet werden, bei dem der Client ein zeitlich begrenztes und signiertes Zugriffstoken von einem Sicherheitstokendienst erhält, ohne den Signaturschlüssel überhaupt zu besitzen.

> [!NOTE]
> Azure Event Hubs unterstützt die Autorisierung gegenüber Event Hubs-Ressourcen mit Azure Active Directory (Azure AD). Das Autorisieren von Benutzern oder Anwendungen mithilfe eines von Azure AD zurückgegebenen OAuth 2.0-Tokens bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung per SAS (Shared Access Signature). Mit Azure AD ist es nicht erforderlich, Token in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen.
>
> Microsoft empfiehlt die Verwendung von Azure AD für Ihre Azure Event Hubs-Anwendungen, wenn dies möglich ist. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md).

> [!IMPORTANT]
> SAS-Token (Shared Access Signatures) sind wichtig, um Ihre Ressourcen zu schützen. SAS sorgt für Granularität und gewährt Clients Zugriff auf Ihre Event Hubs-Ressourcen. Eine öffentliche Freigabe sollte vermieden werden. Falls eine Freigabe aus Problembehandlungsgründen erforderlich ist, sollten Sie die Verwendung einer reduzierten Version von Protokolldateien oder ggf. das Löschen von SAS-Token aus den Protokolldateien in Betracht ziehen und sicherstellen, dass auch die Screenshots keine SAS-Informationen enthalten.

## <a name="shared-access-authorization-policies"></a>SAS-Autorisierungsrichtlinien
Jeder Event Hubs-Namespace und jede Event Hubs-Entität (eine Event Hub-Instanz oder ein Kafka-Thema) verfügt über eine SAS-Autorisierungsrichtlinie, die aus Regeln besteht. Die Richtlinie auf Namespaceebene gilt für alle Entitäten innerhalb des Namespace, unabhängig von ihrer jeweiligen Richtlinienkonfiguration.
Für jede Regel einer Autorisierungsrichtlinie sind drei Angaben erforderlich: Name, Bereich und Rechte. Der Name ist ein eindeutiger Name im Bereich. Der Bereich ist der URI der jeweiligen Ressource. Für einen Event Hubs-Namespace wird als Bereich der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) angegeben, z. B. `https://<yournamespace>.servicebus.windows.net/`.

Die durch die Richtlinienregel gewährten Rechte können eine Kombination aus Folgendem sein:
- **Senden**: Gewährt das Recht zum Senden von Nachrichten an die Entität.
- **Lauschen**: Gewährt für die Entität das Recht zum Lauschen oder Empfangen.
- **Verwalten**: Gewährt das Recht zum Verwalten der Topologie des Namespace, einschließlich Erstellung und Löschung von Entitäten.

> [!NOTE]
> Das Recht **Verwalten** umfasst auch die Rechte **Senden** und **Lauschen**.

Eine Namespace- oder Entitätenrichtlinie kann bis zu 12 SAS-Autorisierungsregeln aufweisen und bietet so Platz für die drei Regelsätze, die jeweils die grundlegenden Rechte und die Kombination aus „Senden“ und „Lauschen“ abdecken. Mit dieser Einschränkung wird verdeutlicht, dass der SAS-Richtlinienspeicher nicht als Benutzer- oder Dienstkontospeicher vorgesehen ist. Wenn Ihre Anwendung Zugriff auf Event Hubs-Ressourcen basierend auf Benutzer- oder Dienstidentitäten gewähren muss, sollte ein Sicherheitstokendienst implementiert werden, der nach einer Authentifizierungs- und Zugriffsprüfung SAS-Token ausstellt.

Einer Autorisierungsregel wird ein **Primärschlüssel** und ein **Sekundärschlüssel** zugewiesen. Hierbei handelt es sich um sichere Kryptografieschlüssel. Es ist wichtig, dass Sie sie nicht verlieren oder offenlegen. Sie sind jederzeit über das Azure-Portal verfügbar. Sie können einen beliebigen der generierten Schlüssel verwenden und die Schlüssel jederzeit erneut generieren. Wenn Sie einen Schlüssel in der Richtlinie neu erstellen oder ändern, sind alle zuvor basierend auf diesem Schlüssel ausgestellten Token sofort ungültig. Ausgehende Verbindungen, die basierend auf solchen Token erstellt wurden, funktionieren allerdings weiterhin, bis das Token abgelaufen ist.

Wenn Sie einen Event Hubs-Namespace erstellen, wird für den Namespace automatisch eine Richtlinie mit dem Namen **RootManageSharedAccessKey** erstellt. Diese Richtlinie verfügt über die Berechtigung **Verwalten** für den gesamten Namespace. Wir empfehlen Ihnen, diese Regel wie ein administratives Stammkonto zu behandeln und nicht in Ihrer Anwendung zu verwenden. Sie können auf der Registerkarte **Konfigurieren** für den Namespace im Portal, mit PowerShell oder über die Azure CLI weitere Richtlinienregeln erstellen.

## <a name="best-practices-when-using-sas"></a>Bewährte Methoden bei Verwendung von SAS
Wenn Sie Shared Access Signatures in Ihren Anwendungen verwenden, müssen Sie sich der potenziellen Risiken bewusst sein:

- Offengelegte SAS können von allen Personen, in deren Besitz sie gelangen, verwendet werden. Dies kann die Sicherheit Ihrer Event Hubs-Ressourcen gefährden.
- Wenn die SAS einer Clientanwendung abläuft und die Anwendung keine neue SAS von Ihrem Dienst abrufen kann, wird die Funktionsweise der Anwendung unter Umständen eingeschränkt.

Mit den folgenden Empfehlungen für die Verwendung von Shared Access Signatures können Sie diese Risiken verringern:

- **Sorgen Sie dafür, dass die Clients die SAS bei Bedarf automatisch erneuern müssen**: Die Clients sollten ihre SAS rechtzeitig vor der Ablaufzeit erneuern, um Zeit für Wiederholungsversuche zuzulassen, falls der entsprechende Dienst nicht verfügbar sein sollte. Falls Ihre SAS für eine kleine Anzahl sofortiger und kurzfristiger Vorgänge gilt, die normalerweise innerhalb des Ablaufzeitraums abgeschlossen werden, ist dies unter Umständen nicht notwendig, da die SAS nicht erneuert werden müssen. Wenn Ihre Clients jedoch immer wieder Anfragen über die SAS stellen, müssen Sie sich mit dem Ablaufmechanismus auseinander setzen. Dabei müssen Sie zwischen der Notwendigkeit der Kurzlebigkeit einer SAS (wie zuvor beschrieben) und der Sicherstellung einer rechtzeitigen Anforderung der Erneuerung durch den Client abwägen. So verhindern Sie, dass die SAS vor einer erfolgreichen Erneuerung abläuft.
- **Gehen Sie bei der SAS-Startzeit mit Bedacht vor**: Wenn Sie die Startzeit für SAS auf **jetzt** festlegen, können aufgrund von Zeitunterschieden zwischen unterschiedlichen Computern in den ersten Minuten Probleme auftreten. Üblicherweise sollten Sie als Startzeit eine Uhrzeit angeben, die mindestens 15 Minuten in der Vergangenheit liegt. Sie können auch so vorgehen, dass Sie keine Startzeit festlegen, damit die Aktivierung immer sofort erfolgt. Dasselbe gilt auch für die Ablaufzeit. Beachten Sie, dass es für alle Anforderungen in beiden Richtungen zu Uhrabweichungen von bis zu 15 Minuten kommen kann. 
- **Geben Sie die Ressource, auf die zugegriffen werden soll, exakt an**: Aus Sicherheitsgründen sollten Benutzer nur die minimal erforderlichen Berechtigungen erhalten. Wenn ein Benutzer nur Lesezugriff auf eine einzige Entität benötigt, dann geben Sie auch nur Lesezugriff auf diese Entität, und nicht Lese-/Schreib-/Löschzugriff auf alle Entitäten. So lässt sich auch der Schaden verringern, wenn eine SAS kompromittiert wurde, weil die SAS dem Angreifer weniger Angriffsmöglichkeiten bietet.
- **Verwenden Sie SAS nicht immer**: Manchmal überwiegen die Risiken eines bestimmten Vorgangs für Ihre Event Hubs-Instanz gegenüber den Vorzügen von SAS. Erstellen Sie für Vorgänge dieser Art einen Dienst auf der mittleren Ebene, der in Ihre Event Hubs-Instanz schreibt, nachdem die Überprüfung der Geschäftsregeln, die Authentifizierung und die Überwachung durchgeführt wurden.
- **Verwenden Sie immer HTTPs**: Verwenden Sie zum Erstellen oder Verteilen einer SAS immer HTTPS. Wenn eine SAS über HTTP weitergegeben und abgefangen wird, kann ein Angreifer diese mit einem Man-in-the-Middle-Angriff auslesen, anschließend im Namen des Benutzers verwenden und somit unter Umständen sensible Daten gefährden oder böswillig beschädigen.

## <a name="conclusion"></a>Zusammenfassung
Shared Access Signatures sind nützlich, um für Ihre Clients eingeschränkte Berechtigungen für Event Hubs-Ressourcen bereitzustellen. Es handelt sich hierbei um einen wichtigen Teil des Sicherheitsmodells für alle Anwendungen, die Azure Event Hubs verwenden. Wenn Sie sich an die in diesem Artikel beschriebenen bewährten Methoden halten, können Sie mit SAS eine größere Flexibilität für den Zugriff auf Ihre Ressourcen erzielen, ohne die Sicherheit Ihrer Anwendung zu gefährden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in den folgenden verwandten Artikeln: 

- [Authentifizieren von Anforderungen an Event Hubs über eine Anwendung mithilfe von Azure Active Directory](authenticate-application.md)
- [Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Event Hubs-Ressourcen](authenticate-managed-identity.md)
- [Authentifizieren von Anforderungen an Azure Event Hubs mithilfe von Shared Access Signature](authenticate-shared-access-signature.md)
- [Autorisieren des Zugriffs auf Event Hubs-Ressourcen mit Azure Active Directory](authorize-access-azure-active-directory.md)


