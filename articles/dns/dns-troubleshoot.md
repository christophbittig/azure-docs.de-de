---
title: 'Azure DNS: Handbuch zur Problembehandlung'
description: In diesem Lernpfad lernen Sie die Problembehandlung der häufigen Probleme mit Azure DNS kennen.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: troubleshooting
ms.date: 09/20/2019
ms.author: rohink
ms.openlocfilehash: e4621b73c8b71ba3bb4b42801de5e306cfa3562e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128573103"
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


## <a name="unhealthy-dns-zones"></a>Fehlerhafte DNS-Zonen

Konfigurationsfehler können dazu führen, dass DNS-Zonen fehlerhaft werden. Im Folgenden ein paar Szenarien, die zu diesem Verhalten führen können:

* **Fehlerhafte Delegierung**: Eine Zone enthält *NS*-Delegierungsdatensätze, mit denen Datenverkehr von primären an die untergeordneten Zonen delegiert werden kann. Wenn einer der *NS*-Einträge in der übergeordneten Zone vorhanden ist, sollte der DNS-Server eigentlich andere Einträge unterhalb der Delegierung maskieren, mit Ausnahme von Glue-Einträgen. Wenn die Zone jedoch andere Datensätze unterhalb der Delegierung enthält, wird die Zone als fehlerhaft gekennzeichnet.

    Die folgende Tabelle enthält Szenarien und die entsprechenden Zonenzustandsergebnisse, wenn eine Zone einen NS-Delegierungsdatensatz enthält.

    | Szenario | Zone enthält</br>NS-Delegierungsdatensatz? | Zone enthält</br>Glue Records? | Zone enthält andere</br>Datensätze unterhalb der</br>Delegierung? | Zonenintegrität |
    |----------|-------------------------------------|-----------------------------|--------------------------------------------------|-------------|
    | 1        | Nein                                  | -                           | -                                                | Healthy     |
    | 2        | Ja                                 | Ja                         | Nein                                               | Healthy     |
    | 3        | Ja                                 | Nein                          | Nein                                               | Healthy     |
    | 4        | Ja                                 | Nein                          | Ja                                              | Fehlerhaft   |
    | 5        | Ja                                 | Ja                         | Ja                                              | Fehlerhaft   |

    **Empfehlung:** Entfernen Sie alle Datensätze mit Ausnahme von Glue-Datensätzen unterhalb der Delegierungsdatensätze in Ihren Zonen.

* **Null TTL** – Die Gültigkeitsdauer (Time to Live, TTL) ist eine Einstellung, die dem DNS-Resolver sagt, wie lange eine Abfrage zwischengespeichert werden soll, bevor eine neue angefordert wird. Die gesammelten Informationen werden dann im Cache des rekursiven oder lokalen Resolvers für die TTL-Dauer gespeichert, bevor das Gerät sich zurück wendet, um neue und aktualisierte Details zu sammeln.

    Wenn TTL in der Konfiguration auf 0 festgelegt ist, kann eines der folgenden Probleme auftreten:

    * Lange Antwort.
    * Anstieg des DNS-Datenverkehrs und der Kosten.
    * Empfindlich für DDoS-Angriffe.

    **Empfehlung**: Stellen Sie sicher, dass der TTL-Wert nicht auf *0* festgelegt ist. 

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
