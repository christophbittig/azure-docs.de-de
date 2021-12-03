---
title: 'Azure DNS: Handbuch zur Problembehandlung'
description: In diesem Lernpfad lernen Sie die Problembehandlung der häufigen Probleme mit Azure DNS kennen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 11/10/2021
ms.author: rohink
ms.openlocfilehash: bc003c640e51b855f446878aa0b5829b2d2c09c5
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400355"
---
# <a name="azure-dns-troubleshooting-guide"></a>Azure DNS – Handbuch zur Problembehandlung

Dieser Artikel enthält Informationen zur Problembehandlung für häufige Fragen zu Azure DNS.

Wenn das Problem durch diese Schritte nicht behoben wird, können Sie auch auf unserer [Frageseite von Microsoft Q&A zu Communitysupport](/answers/topics/azure-virtual-network.html) nach dem Problem suchen oder einen Beitrag dazu veröffentlichen. Sie haben auch die Möglichkeit, eine Azure-Supportanfrage zu erstellen.

## <a name="i-cant-create-a-dns-zone"></a>Ich kann keine DNS-Zone erstellen.

Probieren Sie zum Beheben allgemeiner Probleme die folgenden Schritte aus:

1.  Bestimmen Sie mithilfe der Azure DNS-Überwachungsprotokolle die Fehlerursache.
2.  Jeder DNS-Zonenname muss innerhalb seiner Ressourcengruppe eindeutig sein, d.h. eine Ressourcengruppe kann nicht zwei DNS-Zonen mit dem gleichen Namen enthalten. Verwenden Sie einen anderen Zonennamen oder eine andere Ressourcengruppe.
3.  Unter Umständen wird der Fehler „Die maximale Anzahl an Zonen im Abonnement {Abonnement-ID} wurde erreicht oder überschritten.“ angezeigt. Verwenden Sie ein anderes Azure-Abonnement, löschen Sie einige Zonen, oder wenden Sie sich an den Azure-Support, um Ihr Abonnementlimit zu erhöhen.
4.  Möglicherweise wird der Fehler „Die Zone ‚{Zonenname}‘ ist nicht verfügbar.“ angezeigt. Dieser Fehler weist darauf hin, dass Azure-DNS keine Namenserver für diese DNS-Zone zuordnen konnte. Versuchen Sie, einen anderen Zonennamen zu verwenden. Wenn Sie der Besitzer des Domänennamens sind, können Sie sich auch an den Azure-Support wenden, der die Namenserver für Sie zuordnen kann.

### <a name="recommended-articles"></a>Empfohlene Artikel

* [DNS-Zonen und -Einträge](dns-zones-records.md)
* [Erstellen einer DNS-Zone](./dns-getstarted-portal.md)

## <a name="i-cant-create-a-dns-record"></a>Ich kann keinen DNS-Eintrag erstellen.

Probieren Sie zum Beheben allgemeiner Probleme die folgenden Schritte aus:

1.  Bestimmen Sie mithilfe der Azure DNS-Überwachungsprotokolle die Fehlerursache.
2.  Ist der Eintragssatz bereits vorhanden?  Azure DNS verwaltet Einträge als *Eintragssätze*; dabei handelt es sich um die Sammlung von Einträgen mit dem gleichen Namen und dem gleichen Typ. Wenn ein Eintrag mit dem gleichen Namen und Typ bereits vorhanden ist, müssen Sie zum Hinzufügen eines weiteren solchen Eintrags den vorhandenen Eintragssatz bearbeiten.
3.  Versuchen Sie, einen Eintrag an der DNS-Zonenspitze (den „Stamm“ der Zone) zu erstellen? In diesem Fall sieht die DNS-Konvention die Verwendung des @-Zeichens als Eintragsname vor. Beachten Sie außerdem, dass die DNS-Standards keine CNAME-Einträge an der Zonenspitze zulassen.
4.  Liegt ein CNAME-Konflikt vor?  Die DNS-Standards lassen keinen CNAME-Eintrag mit dem gleichen Namen wie ein Eintrag von einem anderen Typ zu. Wenn bereits ein CNAME-Eintrag vorhanden ist, schlägt das Erstellen eines Eintrags von einem anderem Typ mit gleichem Namen fehl.  Ebenso schlägt das Erstellen eines CNAME-Eintrags fehl, wenn der Name mit dem eines vorhandenen Datensatzes von einem anderen Typ übereinstimmt. Beheben Sie den Konflikt durch Entfernen des anderen Eintrags oder durch Auswählen eines anderen Eintragsnamens.
5.  Haben Sie die Beschränkung für die zulässige Anzahl von Eintragssätzen in einer DNS-Zone erreicht? Die aktuelle sowie die maximale Anzahl von Eintragssätzen werden im Azure-Portal unter den Eigenschaften für die jeweilige Zone angezeigt. Wenn Sie diese Beschränkung erreicht haben, löschen Sie entweder einige Eintragssätze, oder wenden Sie sich an den Azure-Support, um die Eintragssatzbeschränkung für diese Zone zu erhöhen; versuchen Sie es anschließend erneut. 

### <a name="recommended-articles"></a>Empfohlene Artikel

* [DNS-Zonen und -Einträge](dns-zones-records.md)
* [Erstellen einer DNS-Zone](./dns-getstarted-portal.md)

## <a name="i-cant-resolve-my-dns-record"></a>Ich kann meinen DNS-Eintrag nicht auflösen.

Die DNS-Namensauflösung ist ein mehrstufiger Prozess, der aufgrund einer Vielzahl von Gründen fehlschlagen kann. Mithilfe der folgenden Schritte können Sie ermitteln, warum die DNS-Auflösung für einen DNS-Eintrag fehlschlägt, der in Azure DNS gehostet ist.

1.  Vergewissern Sie sich, dass die DNS-Einträge in Azure DNS richtig konfiguriert wurden. Überprüfen Sie die DNS-Einträge im Azure-Portal, und stellen Sie sicher, dass der Zonenname, Eintragsname und Eintragstyp richtig sind.
2.  Vergewissern Sie sich, dass die DNS-Einträge auf den Azure-DNS-Namenservern richtig aufgelöst werden.
    - Wenn Sie DNS-Abfragen über Ihren lokalen Computer vornehmen, werden möglicherweise zwischengespeicherte Ergebnisse angezeigt, die den aktuellen Status der Namenserver nicht wiedergeben.  Zudem verwenden Unternehmensnetzwerke häufig DNS-Proxyserver die verhindern, dass DNS-Abfragen an bestimmte Namenserver geleitet werden.  Verwenden Sie zur Vermeidung dieser Probleme einen webbasierten Namensauflösungsdienst wie [digwebinterface](https://digwebinterface.com).
    - Achten Sie darauf, die richtigen Namenserver für Ihre DNS-Zone anzugeben, wie im Azure-Portal gezeigt.
    - Überprüfen Sie, ob der DNS-Name (Sie müssen den vollqualifizierten Namen einschließlich des Zonennamens angeben) und der Eintragstyp richtig sind.
3.  Vergewissern Sie sich, dass der DNS-Domänenname ordnungsgemäß [an die Azure DNS-Namenserver delegiert wurde](dns-domain-delegation.md). Zahlreiche [Websites von Drittanbietern bieten die Überprüfung von DNS-Delegierungen an](https://www.bing.com/search?q=dns+check+tool). Dieser Test ist eine *Zonen* delegierung, daher sollten Sie nur den Namen der DNS-Zone und nicht den vollqualifizierten Eintragsnamen eingeben.
4.  Nach Abschluss der obigen Schritte sollte Ihr DNS-Eintrag jetzt ordnungsgemäß aufgelöst werden. Um dies zu überprüfen, können Sie [digwebinterface](https://digwebinterface.com) erneut und dieses Mal mit den standardmäßigen Namenservereinstellungen verwenden.

### <a name="recommended-articles"></a>Empfohlene Artikel

* [Delegieren einer Domäne an Azure DNS](dns-domain-delegation.md)

## <a name="dns-zone-status-and-unhealthy-delegation-scenarios"></a>DNS-Zonenstatus und fehlerhafte Delegierungsszenarien

Der DNS-Zonenstatus zeigt den aktuellen Status der Zone an. Der DNS-Zonenstatus kann **Unbekannt,** **Verfügbar** und **Heruntergestuft** sein.

### <a name="unknown"></a>Unbekannt

Wenn eine Ressource neu erstellt wird, sind nicht sofort Integritätssignale für diese neuen Ressourcen verfügbar. Es können maximal 24 Stunden verstreichen, bis die richtigen Integritätssignale für DNS-Zonen eingehen. Bis zu diesem Zeitpunkt wird die Integrität der DNS-Zonen als **Unbekannt** angezeigt.

Wenn die Ressourcen-Integritätsprüfung seit länger als sechs Stunden keine Informationen zu DNS-Zonen erhalten hat, werden die Zonen als „Unbekannt“ markiert. Zwar zeigt dieser Status nicht definitiv den Ressourcenzustand an, aber er ist ein wichtiger Datenpunkt im Problembehandlungsprozess. Sobald das Signal empfangen wurde, und wenn die Ressource wie erwartet ausgeführt wird, wird der Status der Ressource nach wenigen Minuten in **Verfügbar** geändert.

Der folgende Screenshot zeigt ein Beispiel für die Meldung der Ressourcen-Integritätsprüfung.

:::image type="content" source="./media/dns-troubleshoot/unknown-status.png" alt-text="Screenshot des Status „Unbekannt“.":::

### <a name="available"></a>Verfügbar

Der Status **Verfügbar**  zeigt an, dass die Ressourcen-Integritätsprüfung kein Delegierungsproblem in Ihren DNS-Zonen erkannt hat. Dieser Status bedeutet, dass Namenserver-Delegierungseinträge in Ihrer primären Zone ordnungsgemäß verwaltet werden, und dass in Ihrer primären Zone keine Einträge vorhanden sind, die für untergeordnete Zonen vorgesehen sind. 

Der folgende Screenshot zeigt ein Beispiel für die Meldung der Ressourcen-Integritätsprüfung.

:::image type="content" source="./media/dns-troubleshoot/available-status.png" alt-text="Screenshot des Status „Verfügbar“.":::

### <a name="degraded"></a>Heruntergestuft

Der Status **Heruntergestuft** zeigt an, dass die Ressourcen-Integritätsprüfung ein Delegierungsproblem in Ihren DNS-Zonen erkannt hat. Korrigieren Sie die Delegierungskonfiguration, und warten Sie 24 Stunden, bis sich der Status in **Verfügbar** ändert.  

Der folgende Screenshot zeigt ein Beispiel für die Meldung der Ressourcen-Integritätsprüfung.

:::image type="content" source="./media/dns-troubleshoot/degraded-status.png" alt-text="Screenshot des Status „Heruntergestuft“.":::

Wenn nach der Korrektur der Konfiguration 24 Stunden verstrichen sind und die DNS-Zonen weiterhin heruntergestuft sind, wenden Sie sich an den Support.  

### <a name="configuration-error-scenario"></a>Konfigurationsfehlerszenario

Das folgende Szenario demonstriert, in dem ein Konfigurationsfehler zum fehlerhaften Zustand der DNS-Zonen geführt hat.

**Fehlerhafte Delegierung**

Eine primäre Zone enthält Namenserver-Delegierungseinträge, mit denen Datenverkehr von der primären an die untergeordneten Zonen delegiert werden kann. Wenn ein Namenserver-Delegierungseintrag in der übergeordneten Zone vorhanden ist, soll der DNS-Server alle anderen Einträge unterhalb des Namenserver-Delegierungseintrags maskieren, mit Ausnahme von Glue-Einträgen, und Datenverkehr auf Grundlage der Benutzerabfrage an die entsprechende untergeordnete Zone weiterleiten. Wenn eine übergeordnete Zone andere Einträge enthält, die für die untergeordneten Zonen (delegierten Zonen) unterhalb des Namenserver-Delegierungseintrags vorgesehen sind, wird die Zone als fehlerhaft markiert, und ihr Status lautet **Heruntergestuft**.

**Was sind Glue-Einträge?** – Hierbei handelt es sich um Einträge unterhalb des Delegierungseintrags, die bei der Weiterleitung von Datenverkehr an die delegierten/untergeordneten Zonen mithilfe ihrer IP-Adressen helfen und, wie im Folgenden zu sehen, konfiguriert werden.

| Einstellung | Wert |
| ------- | ----- |
| **Zone** | contoso.com |
| **Delegierungseintrag** | Untergeordneter Namenserver </br> ns1.child.contoso.com |
| **Glue-Eintrag** | ns1.child A 1.1.1.1 |

#### <a name="example-of-an-unhealthy-zone"></a>Beispiel für eine fehlerhafte Zone

Im Folgenden sehen Sie ein Beispiel für eine Zone, die Einträge unterhalb der Namenserverdelegierung enthält.

* Zonenname: contoso.com

| Name | type | TTL | Wert |
| ---- | ---- | --- | ----- |
| @ | NS | 3600 | ns1-04.azure-dns.com. |
| @ | SOA | 3600 | _SOA-Werte_ |
| * | A | 3600 | 255.255.255.255 |
| **child** | **NS** | **3600** | **ns1-08.azure-dns.com** (Namenserver-Delegierungseintrag) |
| _**foo.child**_ | _**A**_ | _**3600**_ | _**10.10.10.10**_ |
| _**txt.child**_ | _**TXT**_ | _**3600**_ | _**"Text-Eintrag"**_ |
| abc.test | A | 3600 | 5.5.5.5 |

Im vorherigen Beispiel sind **child** (untergeordnet) die Namenserver-Delegierungseinträge. Die Einträge _**foo.child**_ und _**txt.child**_ sind Einträge, die nur in der untergeordneten Zone, **child.contoso.com.** , vorhanden sein sollten. Diese Einträge können zu Inkonsistenzen führen, wenn sie nicht aus der übergeordneten Zone, **contoso.com**, entfernt werden. Diese Inkonsistenzen können dazu führen, dass die Zone als fehlerhaft betrachtet wird, mit dem Status **Heruntergestuft**.

#### <a name="examples-of-when-a-zone-is-considered-healthy-or-unhealthy"></a>Beispiele dafür, wann eine Zone als fehlerfrei oder fehlerhaft betrachtet wird

| Beispiel | Status |
| ------- | ------ |
| Die Zone enthält keine Namenserver-Delegierungseinträge, Glue-Einträge oder anderen Einträge. | **Healthy** |
| Die Zone enthält nur Namenserver-Delegierungseinträge. | **Healthy** |
| Die Zone enthält nur Namenserver-Delegierungseinträge und Glue-Einträge. | **Healthy** |
| Die Zone enthält Namenserver-Delegierungseinträge und andere Einträge (mit Ausnahme von Glue-Einträgen) unterhalb des Delegierungseintrags, der in der untergeordneten Zone vorhanden sein sollte. | **Fehlerhaft** |
| Die Zone enthält Namenserver-Delegierungseinträge, Glue-Einträge und andere Einträge (mit Ausnahme von Glue-Einträgen). | **Fehlerhaft** |

**Wie können Sie diesen Zustand beheben?** – Zum Beheben suchen und entfernen Sie alle Einträge, außer Glue-Einträgen, unterhalb der Namenserver-Delegierungseinträge in Ihrer übergeordneten Zone.

**Auffinden fehlerhafter Delegierungseinträge** – Es wurde ein Skript erstellt, um die fehlerhaften Delegierungseinträge in Ihrer Zone zu finden.  Das Skript meldet Einträge, die fehlerhaft sind.

1. Speichern Sie das Skript, das sich unter [Suchen von fehlerhaften DNS-Einträgen in Azure DNS: PowerShell-Skriptbeispiel](./scripts/find-unhealthy-dns-records.md) befindet.

2. Führen Sie das Skript wie im Skript-Editor erwähnt aus.  Das Skript kann bearbeitet werden, um es an Ihre Anforderungen anzupassen.

**Verlaufsinformationen**: Im Abschnitt „Integritätsverlauf“ von Resource Health stehen bis zu 14 Tage alte Integritätsverlaufsdaten zur Verfügung. Dieser Abschnitt enthält auch die Ursache für die von Resource Health gemeldeten Downtimes (sofern verfügbar). Derzeit zeigt Azure die Downtime für Ihre DNS-Zonenressource mit einer Granularität von 24 Stunden an.

## <a name="how-do-i-specify-the-service-and-protocol-for-an-srv-record"></a>Wie gebe ich den „service“ und „protocol“ für einen SRV-Eintrag an?

Azure DNS verwaltet DNS-Einträge als Eintragssätze – die Sammlung von Einträgen mit dem gleichen Namen und dem gleichen Typ. Für einen SVR-Eintragssatz müssen die Parameter „service“ und „protocol“ als Teil des Namens des Eintragssatzes angegeben werden. Die anderen SRV-Parameter („priority“, „weight“, „port“ und „target“) werden separat für jeden Eintrag im Eintragssatz angegeben.

Beispiele für SRV-Eintragsnamen (Dienstname „sip“, Protokoll „tcp“):

- \_sip.\_tcp (erstellt einen Eintragssatz an der Zonenspitze)
- \_sip.\_tcp.sipservice (erstellt einen Eintragssatz mit dem Namen „sipservice“)

### <a name="recommended-articles"></a>Empfohlene Artikel

* [DNS-Zonen und -Einträge](dns-zones-records.md)
* [Erstellen von DNS-Ressourceneintragssätzen und -Einträgen mit dem Azure-Portal](./dns-getstarted-portal.md)
* [SRV-Eintragstyp (Wikipedia)](https://en.wikipedia.org/wiki/SRV_record)

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu [Azure DNS-Zonen und -Einträgen](dns-zones-records.md)
* Lernen Sie, wie Sie [eine DNS-Zone erstellen](./dns-getstarted-portal.md) und [DNS-Einträge erstellen](./dns-getstarted-portal.md), um mit der Verwendung von Azure DNS zu beginnen.
* Um eine vorhandene DNS-Zone zu migrieren, sollten Sie sich zuerst darüber informieren, wie Sie [eine DNS-Zonendatei importieren und exportieren](dns-import-export.md).
