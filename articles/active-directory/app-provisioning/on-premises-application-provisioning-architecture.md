---
title: Azure AD-Architektur für die lokale Anwendungsbereitstellung | Microsoft-Dokumentation
description: Die Dokumentation enthält eine Übersicht über die Architektur für die lokale Anwendungsbereitstellung.
services: active-directory
author: billmath
manager: karenh444
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89a0f2bd6b48d195836303a89143593c4d959bfb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028004"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Azure AD-Architektur für die lokale Anwendungsbereitstellung

>[!IMPORTANT]
> Die Vorschauversion der lokalen Bereitstellung ist derzeit nur auf Einladung verfügbar. Um den Zugriff auf die Funktion anzufordern, verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess). Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit (GA) in den nächsten Monaten für weitere Kunden und Connectors.

## <a name="overview"></a>Übersicht

Das folgende Diagramm enthält eine Übersicht über die Funktionsweise der lokalen Anwendungsbereitstellung.

![Ein Diagramm, das die Architektur für die lokale Anwendungsbereitstellung zeigt.](.\media\on-premises-application-provisioning-architecture\arch-3.png)

Es gibt drei Hauptkomponenten für die Bereitstellung von Benutzern in einer lokalen Anwendung:

- Der Bereitstellungs-Agent ermöglicht die Konnektivität zwischen Azure Active Directory (Azure AD) und Ihrer lokalen Umgebung.
- Der ECMA-Host konvertiert Bereitstellungsanforderungen von Azure AD in Anforderungen, die an Ihre Zielanwendung gesendet werden. Er dient als Gateway zwischen Azure AD und Ihrer Anwendung. Mit dem Host können Sie vorhandene ECMA2-Connectors importieren, die mit dem Microsoft Identity Manager verwendet werden. Der ECMA-Host ist nicht erforderlich, wenn Sie eine SCIM-Anwendung oder ein SCIM-Gateway erstellt haben.
- Der Azure AD-Bereitstellungsdienst dient als Synchronisierungs-Engine.

>[!NOTE]
> Eine Synchronisierung mit dem Microsoft Identity Manager ist nicht erforderlich. Sie können sie aber verwenden, um Ihren ECMA-Connector zu erstellen und zu testen, bevor Sie diesen auf den ECMA-Host importieren.


### <a name="firewall-requirements"></a>Firewallanforderungen

Sie müssen keine eingehenden Verbindungen mit dem Unternehmensnetzwerk öffnen. Für die Bereitstellungs-Agenten werden nur ausgehende Verbindungen mit dem Bereitstellungsdienst verwendet. Dies bedeutet, dass keine Firewallports für eingehende Verbindungen geöffnet werden müssen. Sie benötigen auch kein Umkreisnetzwerk (DMZ), da alle Verbindungen in ausgehender Richtung verlaufen und über einen sicheren Kanal erfolgen.

## <a name="ecma-connector-host-architecture"></a>Architektur des ECMA-Connectorhosts
Der ECMA-Connectorhost umfasst mehrere Bereiche, die zum Erzielen einer lokalen Bereitstellung verwendet werden.  Diese Bereiche werden in der folgenden konzeptionellen Abbildung dargestellt.  In der nachstehenden Tabelle werden die Bereiche ausführlicher beschrieben.

[![ECMA-Connectorhost](.\media\on-premises-application-provisioning-architecture\ecma-2.png)](.\media\on-premises-application-provisioning-architecture\ecma-2.png#lightbox)



|Bereich|BESCHREIBUNG|
|-----|-----|
|Endpunkte|Verantwortlich für die Kommunikation mit und die Übertragung von Daten zum Azure AD-Bereitstellungsdienst.|
|In-Memory-Cache|Wird verwendet, um die aus der lokalen Datenquelle importierten Daten zu speichern.|
|Automatische Synchronisierung|Ermöglicht eine asynchrone Datensynchronisierung zwischen dem ECMA-Connectorhost und der lokalen Datenquelle.|
|Geschäftslogik|Wird verwendet, um alle ECMA-Connectorhostaktivitäten zu koordinieren.  Der Zeitpunkt für die automatische Synchronisierung kann im ECMA-Host auf der Seite mit den Eigenschaften konfiguriert werden.|

### <a name="about-anchor-attributes-and-distinguished-names"></a>Grundlegendes zu Ankerattributen und DNs (Distinguished Names)
Die folgenden Informationen dienen zur besseren Erläuterung der Ankerattribute und der Distinguished Names (DNs), die insbesondere vom genericSQL-Connector verwendet werden.

Das Ankerattribut ist ein eindeutiges Attribut eines Objekttyps, der sich nicht ändert und dieses Objekt im In-Memory-Cache des ECMA-Connectorhosts repräsentiert.

Der Distinguished Name (DN) ist ein Name, der ein Objekt eindeutig identifiziert, indem er dessen aktuellen Speicherort in der Verzeichnishierarchie angibt.  Im Falle von SQL wird der Speicherort innerhalb der Partition angegeben. Der Name wird durch Verketten des Ankerattributs am Stamm der Verzeichnispartition gebildet. 

Bei klassischen DNs in einem herkömmlichen Format, z. B. für Active Directory oder LDAP, stellen wir uns einen Namen ähnlich dem folgenden vor:

  `CN=Lola Jacobson,CN=Users,DC=contoso,DC=com`

Für eine Datenquelle wie beispielsweise SQL, die flach und nicht hierarchisch aufgebaut ist, muss der DN jedoch entweder bereits in einer der Tabellen vorhanden sein oder anhand der Informationen erstellt werden, die wir dem ECMA-Connectorhost bereitstellen.  

Erreicht wird dies, indem wir beim Konfigurieren des genericSQL-Connectors das Kontrollkästchen **Automatisch generiert** aktivieren. Wenn festgelegt wurde, dass der DN automatisch erzeugt werden soll, generiert der ECMA-Host einen DN im LDAP-Format: CN=&lt;anchorvalue&gt;,OBJECT=&lt;type&gt;. Dies setzt auch voraus, dass auf der Seite für die Konnektivität die Option „DN ist Anker“ **deaktiviert** wurde. 
 
 [![„DN ist Anker“ deaktiviert](.\media\on-premises-application-provisioning-architecture\user-2.png)](.\media\on-premises-application-provisioning-architecture\user-2.png#lightbox)

Der genericSQL-Connector erwartet, dass der DN im LDAP-Format aufgefüllt wird.  Der genericSQL-Connector verwendet das LDAP-Format mit dem Komponentennamen „OBJECT=“. Dies ermöglicht die Verwendung von Partitionen (jeder Objekttyp ist eine Partition).

Da der ECMA-Connectorhost derzeit nur den USER-Objekttyp unterstützt, wird der OBJECT=&lt;type&gt; als OBJECT=USER festgelegt.  Der DN für einen Benutzer mit dem Ankerwert „ljacobson“ würde also folgendermaßen lauten:

  CN=ljacobson,OBJECT=USER


### <a name="user-creation-workflow"></a>Workflow für die Benutzererstellung

1.  Der Azure AD-Bereitstellungsdienst fragt den ECMA-Connectorhost ab, um zu ermitteln, ob der Benutzer vorhanden ist.  Hierbei wird das **Attribut für den Abgleich** als Filter verwendet  Dieses Attribut wird im Azure AD-Portal unter „Unternehmensanwendungen“ > „Lokale Bereitstellung“ > „Bereitstellung“ > „Attribut für Abgleich“ definiert.  Es wird mit dem Wert 1 für die Rangfolge für den Abgleich gekennzeichnet.
Sie können ein oder mehrere Attribute für den Abgleich definieren und sie anhand der Rangfolge priorisieren.  Es ist auch möglich, das Attribut für den Abgleich zu ändern.
 [![Attribut für den Abgleich](.\media\on-premises-application-provisioning-architecture\match-1.png)](.\media\on-premises-application-provisioning-architecture\match-1.png#lightbox)

2.  Der ECMA-Connectorhost empfängt die GET-Anforderung und fragt den internen Cache ab, um zu ermitteln, ob der Benutzer vorhanden ist und importiert wurde.  Dies geschieht mithilfe des **Abfrageattributs**. Das Abfrageattribut wird auf der Seite der Objekttypen definiert.  
 [![Abfrageattribut](.\media\on-premises-application-provisioning-architecture\match-2.png)](.\media\on-premises-application-provisioning-architecture\match-2.png#lightbox)


3. Wenn der Benutzer nicht vorhanden ist, sendet Azure AD eine POST-Anforderung, um den Benutzer zu erstellen.  Der ECMA-Connectorhost antwortet Azure AD mit HTTP 201 und gibt eine ID für den Benutzer an. Diese ID wird vom Ankerwert abgeleitet, der auf der Seite der Objekttypen definiert ist. Azure AD verwendet diesen Anker in nachfolgenden Anforderungen, um den ECMA-Connectorhost abzufragen. 
4. Wenn der Benutzer in Azure AD geändert wird, sendet Azure AD eine GET-Anforderung zum Abrufen des Benutzers. Hierbei wird anstelle des Attributs für den Abgleich aus Schritt 1 der Anker aus dem vorherigen Schritt verwendet. So kann z. B. der UPN geändert werden, ohne dass die Verbindung zwischen dem Benutzer in Azure AD und in der App unterbrochen wird.  


## <a name="agent-best-practices"></a>Bewährte Methoden für den Agent
- Vergewissern Sie sich, dass für den Azure AD Connect-Bereitstellungs-Agent der Dienst für automatische Aktualisierungen ausgeführt wird. Er ist standardmäßig aktiviert, wenn Sie den Agent installieren. Die automatische Aktualisierung ist erforderlich, damit Microsoft den Support für Ihre Bereitstellung leisten kann.
- Vermeiden Sie alle Arten von Inline-Untersuchungen der ausgehenden TLS-Kommunikation zwischen den Agents und Azure. Diese Art der Inline-Untersuchung führt zu einer Verschlechterung des Kommunikationsflusses.
- Der Agent muss sowohl mit Azure als auch mit Ihren Anwendungen kommunizieren. Daher wirkt sich die Platzierung des Agenten auf die Latenz dieser beiden Verbindungen aus. Sie können die Wartezeit des End-to-End-Datenverkehrs verringern, indem Sie die einzelnen Netzwerkverbindungen optimieren. Jede Verbindung kann wie folgt optimiert werden:
  - Reduzieren Sie den Abstand zwischen den beiden Enden des Hops.
  - Wählen Sie das richtige zu durchlaufende Netzwerk. Wenn beispielsweise, anstelle des öffentlichen Internets beispielsweise ein privates Netzwerk durchlaufen wird, kann dies aufgrund von dedizierten Links schneller gehen.



## <a name="provisioning-agent-questions"></a>Fragen zum Bereitstellungs-Agent
Hier werden einige häufig gestellte Fragen beantwortet.

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wie lautet die allgemein verfügbare Version des Bereitstellungs-Agenten?

Die neueste GA-Version des Bereitstellungs-Agenten finden Sie unter [Azure AD Connect Bereitstellungs-Agent: Versionsveröffentlichungsverlauf](provisioning-agent-release-version-history.md).

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Wie kann ich die Version meines Bereitstellungs-Agent ermitteln?

 1. Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
 2. Wechseln Sie zur **Systemsteuerung** > **Deinstallieren oder Ändern eines Programms**.
 3. Suchen Sie nach der Version für den Eintrag **Microsoft Azure AD Connect-Bereitstellungs-Agent**.

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Überträgt Microsoft automatisch Updates des Bereitstellungs-Agent per Push?

Ja. Der Bereitstellungs-Agent wird von Microsoft automatisch aktualisiert, wenn der Windows-Dienst Microsoft Azure AD Connect Agent Updater aktiv ist und ausgeführt wird. Die Sicherstellung, dass Ihr Agent auf dem aktuellen Stand ist, ist erforderlich, damit der Support die Problembehandlung durchführen kann.

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>Kann ich den Bereitstellungs-Agent auf demselben Server installieren, auf dem Azure AD Connect oder Microsoft Identity Manager ausgeführt wird?

Ja. Sie können den Bereitstellungs-Agent auf demselben Server installieren, auf dem Azure AD Connect oder der Microsoft Identity Manager ausgeführt wird, aber das ist nicht zwingend erforderlich.

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Wie konfiguriere ich den Bereitstellungs-Agent, damit dieser einen Proxyserver für die ausgehende HTTP-Kommunikation verwendet?

Der Bereitstellungs-Agent unterstützt die Verwendung von Proxys für ausgehenden Datenverkehr. Das können Sie durch das Bearbeiten der Konfigurationsdatei des Agenten **C:\Programm Dateien\Microsoft Azure AD Connect Bereitstellungs-Agent\AADConnectProvisioningAgent.exe.config** konfigurieren. Fügen Sie in der Datei gegen Ende unmittelbar vor dem schließenden `</configuration>` Tag die folgenden Zeilen ein. Ersetzen Sie die Variablen `[proxy-server]` und `[proxy-port]` durch den Namen Ihres Proxyservers und die Portwerte.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Wie stelle ich sicher, dass der Bereitstellungs-Agent mit dem Azure AD-Mandanten kommunizieren kann und dass keine Firewalls die vom Agenten benötigten Ports blockieren?

Sie können auch überprüfen, ob alle erforderlichen Ports geöffnet sind.

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Wie deinstalliere ich den Bereitstellungs-Agent?
 1. Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
 2. Wechseln Sie zur **Systemsteuerung** > **Deinstallieren oder Ändern eines Programms**.
 3. Deinstallieren Sie die folgenden Programme:
     - Microsoft Azure AD Connect-Bereitstellungs-Agent
     - Microsoft Azure AD Connect Agent Updater
     - Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket





## <a name="next-steps"></a>Nächste Schritte

- [App-Bereitstellung](user-provisioning.md)
