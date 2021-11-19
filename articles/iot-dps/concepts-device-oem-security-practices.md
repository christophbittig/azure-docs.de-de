---
title: Sicherheitsmaßnahmen für Hersteller – Azure IoT Device Provisioning-Dienst
description: Bietet einen Überblick über allgemeine Sicherheitsmaßnahmen für OEMs und Gerätehersteller, die Geräte für die Registrierung im Azure IoT Device Provisioning-Dienst (Azure IoT Device Provisioning Service, DPS) vorbereiten.
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 140f15f8940f61c14994b8e72a78c1b5b4f360f2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327099"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Sicherheitsmaßnahmen für Hersteller von Azure IoT-Geräten
Weil immer mehr Hersteller IoT-Geräte freigeben, ist es hilfreich, eine Anleitung rund um gängige Praktiken zu haben. In diesem Artikel werden empfohlene Sicherheitsmaßnahmen zusammengefasst, die Sie bei der Fertigung von Geräten für den Einsatz beim Azure IoT Device Provisioning-Dienst (DPS) beachten müssen.  

> [!div class="checklist"]
> * Auswählen von Optionen für Geräteauthentifizierung
> * Installieren von Zertifikaten auf IoT-Geräten
> * Integrieren eines Trusted Platform Module (TPM) in den Fertigungsprozess

## <a name="selecting-device-authentication-options"></a>Auswählen von Optionen für Geräteauthentifizierung
Das oberste Ziel jeder Sicherheitsmaßnahme für IoT-Geräte ist die Erstellung einer sicheren IoT-Lösung. Aber Probleme wie Hardwareeinschränkungen, Kosten und das Maß an Sicherheitskompetenz wirken sich darauf aus, welche Optionen Sie auswählen. Außerdem wirkt sich Ihr Sicherheitsansatz darauf aus, wie Ihre IoT-Geräte eine Verbindung mit der Cloud herstellen. Während [mehrere Elemente von IoT-Sicherheit](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) zu berücksichtigen sind, muss sich jeder Kunde mit einem wichtigen Element beschäftigen, nämlich dem zu verwendenden Authentifizierungstyp. 

Drei häufig verwendete Authentifizierungstypen sind X.509-Zertifikate, Trusted Platform Modules (TPM) und symmetrische Schlüssel. Obwohl es weitere Authentifizierungstypen gibt, verwenden die meisten Kunden, die Lösungen in Azure IoT erstellen, einen dieser drei Typen. Im weiteren Verlauf dieses Artikels finden Sie einen Überblick über die Vor- und Nachteile bei der Verwendung des jeweiligen Authentifizierungstyps.

### <a name="x509-certificate"></a>X.509-Zertifikat
X.509-Zertifikate sind eine Art von digitaler Identität, die Sie für die Authentifizierung verwenden können. Der X.509-Zertifikatstandard ist in [IETF RFC 5280](https://tools.ietf.org/html/rfc5280) dokumentiert. In Azure IoT gibt es zwei Möglichkeiten zum Authentifizieren von Zertifikaten:
- Fingerabdruck. Ein Fingerabdruckalgorithmus wird für ein Zertifikat ausgeführt, um eine hexadezimale Zeichenfolge zu generieren. Die generierte Zeichenfolge ist ein eindeutiger Bezeichner für das Zertifikat. 
- CA-Authentifizierung (Authentifizierung per Zertifizierungsstelle) basierend auf einer vollständigen Kette. Eine Zertifikatkette ist eine hierarchische Liste aller Zertifikate, die zum Authentifizieren eines EE-Zertifikats (End Entity, Endeinheit) benötigt werden. Für die Authentifizierung eines EE-Zertifikats muss jedes Zertifikat in der Kette – einschließlich einer vertrauenswürdigen Stammzertifizierungsstelle – authentifiziert werden. 

Vorteile bei X.509:
- X.509 ist der sicherste Authentifizierungstyp, der in Azure IoT unterstützt wird.
- X.509 ermöglicht ein hohes Maß an Kontrolle für Zertifikatverwaltungszwecke.
- Viele Anbieter stehen zur Bereitstellung von X.509-basierten Authentifizierungslösungen zur Verfügung.

Nachteile bei X.509:
- Viele Kunden müssen sich möglicherweise auf externe Anbieter für ihre Zertifikate verlassen.
- Die Zertifikatverwaltung kann teuer sein und erhöht die Gesamtkosten für die Lösung.
- Die Verwaltung des Zertifikatlebenszyklus kann schwierig sein, wenn die Logistik nicht gut durchdacht wurde. 

### <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)
TPM, auch bekannt als [ISO/IEC 11889](https://www.iso.org/standard/66510.html), ist ein Standard zum sicheren Generieren und Speichern von kryptografischen Schlüsseln. TPM bezieht sich auch auf ein virtuelles oder physisches E/A-Gerät, das mit Modulen interagiert, die den Standard implementieren. Ein TPM-Gerät kann eigenständige Hardware, integrierte Hardware, ein firmwarebasiertes oder softwarebasiertes Modul sein. 

Es gibt zwei wichtige Unterschiede zwischen TPMs und symmetrischen Schlüsseln: 
- TPM-Chips können auch X.509-Zertifikate speichern.
- Der TPM-Nachweis in DPS verwendet den TPM Endorsement Key (EK), eine Form von asymmetrischer Authentifizierung. Bei asymmetrischer Authentifizierung wird ein öffentlicher Schlüssel für die Verschlüsselung und ein separater privater Schlüssel für die Entschlüsselung verwendet. Im Gegensatz dazu verwenden symmetrische Schlüssel symmetrische Authentifizierung, bei der der private Schlüssel sowohl für die Verschlüsselung als auch die Entschlüsselung verwendet wird. 

Vorteile bei TPM:
- TPMs sind als Standardhardware auf vielen Windows-Geräten mit integrierter Unterstützung für das Betriebssystem enthalten. 
- Der TPM-Nachweis ist einfacher zu sichern als der SAS-tokenbasierte (Shared Access Signature) symmetrische Schlüsselnachweis.
- Sie können Geräteanmeldeinformationen problemlos ablaufen lassen, erneuern oder registrieren. DPS implementiert die IoT Hub-Anmeldeinformationen automatisch immer dann, ein TPM-Gerät erneut bereitgestellt werden muss.

Nachteile bei TPM: 
- TPMs sind komplex und ihre Verwendung kann schwierig sein. 
- Die Anwendungsentwicklung mit TPMs ist schwierig, außer wenn Sie über ein physisches TPM oder einen Qualitätsemulator verfügen.
- Möglicherweise müssen Sie das Board Ihres Geräts umgestalten, damit ein TPM in die Hardware einbezogen wird. 
- Wenn Sie den EK auf einem TPM implementieren, zerstört er dessen Identität und erstellt eine neue. Obwohl der physische Chip unverändert bleibt, hat er in Ihrer IoT-Lösung eine neue Identität.

### <a name="symmetric-key"></a>Symmetrischer Schlüssel
Bei symmetrischen Schlüsseln wird derselbe Schlüssel zum Verschlüsseln und Entschlüsseln von Nachrichten verwendet. Deshalb ist derselbe Schlüssel sowohl dem Gerät als auch dem Dienst bekannt, der ihn authentifiziert. Azure IoT unterstützt SAS-tokenbasierte symmetrische Schlüsselverbindungen. Die Authentifizierung mit einem symmetrischen Schlüssel erfordert eine erhebliche Verantwortung des Besitzers, um die Schlüssel zu sichern und eine gleiche Sicherheitsstufe wie die X.509-Authentifizierung zu erzielen. Wenn Sie symmetrische Schlüssel verwenden, empfiehlt es sich, die Schlüssel mithilfe eines Hardwaresicherheitsmoduls (HSM) zu schützen.

Vorteile bei symmetrischem Schlüssel:
- Die Verwendung symmetrischer Schlüssel ist die einfachste und kostengünstigste Möglichkeit für den Einstieg in die Authentifizierung.
- Durch die Verwendung symmetrischer Schlüssel wird Ihr Prozess optimiert, weil nichts Zusätzliches generiert werden muss. 

Nachteile bei symmetrischem Schlüssel: 
- Symmetrische Schlüssel erfordern einen beträchtlichen Aufwand für ihre Sicherung. Derselbe Schlüssel wird von Gerät und Cloud gemeinsam genutzt. Dies bedeutet, dass der Schlüssel an zwei Stellen geschützt werden muss. Im Gegensatz dazu besteht die Herausforderung bei TPM- und X.509-Zertifikaten darin, den Besitz des öffentlichen Schlüssels nachzuweisen, ohne den privaten Schlüssel offenzulegen.
- Symmetrische Schlüssel verleiten zur Befolgung schlechter Sicherheitsmaßnahmen. Bei symmetrischen Schlüsseln gibt es eine häufige Tendenz zur Hartcodierung von unverschlüsselten Schlüsseln auf Geräten. Obwohl diese Vorgehensweise praktisch ist, bleiben die Schlüssel dabei anfällig. Sie können einen Teil dieses Risikos verringern, indem Sie den symmetrischen Schlüssel auf dem Gerät sicher speichern. Wenn Ihre Priorität letztendlich aber Sicherheit und nicht Bequemlichkeit ist, verwenden Sie für die Authentifizierung X.509-Zertifikate oder TPM. 

### <a name="shared-symmetric-key"></a>Gemeinsam verwendeter symmetrischer Schlüssel
Es gibt eine Variante der symmetrischen Schlüsselauthentifizierung, die als „gemeinsam verwendeter symmetrischer Schlüssel“ bezeichnet wird. Bei diesem Ansatz muss derselbe symmetrische Schlüssel auf allen Geräten verwendet werden. Es wird jedoch empfohlen, dass Sie den Einsatz von gemeinsam verwendeten symmetrischen Schlüsseln auf Ihren Geräten vermeiden. 

Vorteil bei gemeinsam verwendetem symmetrischem Schlüssel:
- Einfache Implementierung und kostengünstige Erstellung im großen Stil. 

Nachteile bei gemeinsam verwendetem symmetrischem Schlüssel: 
- Sehr anfällig für Angriffe. Der Vorteil einer einfachen Implementierung wird durch das Risiko weit übertroffen. 
- Jeder Benutzer kann die Identität Ihrer Geräte annehmen, wenn er den gemeinsam verwendeten Schlüssel erhält.
- Wenn Sie sich auf einen freigegebenen symmetrischen Schlüssel verlassen, der gefährdet ist, verlieren Sie wahrscheinlich die Kontrolle über die Geräte. 

### <a name="making-the-right-choice-for-your-devices"></a>Treffen der richtigen Wahl für Ihre Geräte
Berücksichtigen Sie bei der Auswahl einer Authentifizierungsmethode unbedingt die Vorteile und Kosten jedes Ansatz für Ihren eindeutigen Fertigungsprozess.  Bei der Geräteauthentifizierung gibt es normalerweise ein inverses Verhältnis zwischen der Art und Weise, wie sicher ein bestimmter Ansatz und wie praktisch er ist.  

## <a name="installing-certificates-on-iot-devices"></a>Installieren von Zertifikaten auf IoT-Geräten
Wenn Sie X.509-Zertifikate zur Authentifizierung Ihrer IoT-Geräte verwenden, enthält dieser Abschnitt Anleitungen zum Integrieren von Zertifikaten in Ihren Fertigungsprozess. Sie müssen mehrere Entscheidungen treffen.  Dazu gehören Entscheidungen zu allgemeinen Zertifikatvariablen sowie den Zeitpunkten zum Generieren und Installieren von Zertifikaten. 

Wenn Sie die Verwendung von Kennwörtern gewohnt sind, fragen Sie sich möglicherweise, warum Sie nicht dasselbe Zertifikat in allen Ihren Geräten genauso verwenden können, wie Sie dasselbe Kennwort in allen Ihren Geräten verwenden könnten. Erstens ist es gefährlich, dasselbe Kennwort überall zu verwenden. Die Praxis hat Unternehmen größeren DDoS-Angriffen ausgesetzt – einschließlich des Angriffs, der DNS vor mehreren Jahren an der amerikanischen Ostküste hat zusammenbrechen lassen. Verwenden Sie niemals überall dasselbe Kennwort, auch nicht bei persönlichen Konten. Zweitens ist ein Zertifikat kein Kennwort, sondern eine eindeutige Identität. Ein Kennwort ist vergleichbar mit einem geheimen Code, mit dem jeder eine Tür in einem gesicherten Gebäude öffnen kann.  Es ist etwas, das Sie wissen, und Sie könnten das Kennwort jeder beliebigen Person mitteilen, damit sie Zugriff erhält.  Ein Zertifikat ist wie ein Führerschein mit Ihrem Foto und anderen Details, den Sie einem Wächter zeigen können, um in ein gesichertes Gebäude zu gelangen. Es ist an die Person gebunden, die Sie sind.  Vorausgesetzt, dass der Wächter Personen mit Führerscheinen exakt abgleicht, können nur Sie Ihre Lizenz (Identität) nutzen, um eingelassen zu werden. 

### <a name="variables-involved-in-certificate-decisions"></a>Mit Zertifikatentscheidungen verbundene Variablen
Sehen Sie sich die folgenden Variablen an, und erfahren Sie, wie sich jede einzelne auf den gesamten Fertigungsprozess auswirkt. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Quelle des Zertifikat-Vertrauensankers
Die Verwaltung einer Public Key-Infrastruktur (PKI) kann teuer und komplex sein.  Dies gilt insbesondere, wenn Ihr Unternehmen keine Erfahrung mit der Verwaltung einer PKI hat. Die Optionen sind wie folgt:
- Verwendung einer Drittanbieter-PKI. Sie können Zwischensignaturzertifikate bei einem Drittanbieter von Zertifikaten erwerben. Oder Sie können eine private Zertifizierungsstelle (CA) nutzen. 
- Verwendung einer selbstverwalteten PKI. Sie können Ihr eigenes PKI-System verwalten und eigene Zertifikate generieren.
- Verwenden des [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/)-Sicherheitsdiensts. Diese Option gilt nur für Azure Sphere-Geräte. 

#### <a name="where-certificates-are-stored"></a>Speicherort von Zertifikaten
Es gibt ein paar Faktoren, die sich auf die Entscheidung auswirken, wo Zertifikate gespeichert werden sollen. Zu diesen Faktoren gehören der Gerätetyp, erwartete Renditen (d. h., ob Sie sich ein sicheres Speichern leisten können), Gerätefunktionen und vorhandene Sicherheitstechnologie auf dem Gerät, das Sie möglicherweise verwenden können. Ziehen Sie folgende Möglichkeiten in Betracht:
- In einem Hardwaresicherheitsmodul (HSM). Die Verwendung eines HSM wird dringend empfohlen. Überprüfen Sie, ob auf der Steuerplatine Ihres Geräts bereits ein HSM installiert ist. Wenn Sie wissen, dass Sie kein HSM haben, wenden Sie sich an Ihren Hardwarehersteller, um ein HSM zu finden, das Ihren Anforderungen entspricht.
- An einem sicheren Ort auf dem Datenträger, z. B. in einer vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE).
- Im lokalen Dateisystem oder in einem Zertifikatspeicher. Beispielsweise im Windows-Zertifikatspeicher. 

#### <a name="connectivity-at-the-factory"></a>Konnektivität in der Factory
Konnektivität in der Factory bestimmt, wie und wann Sie die Zertifikate zur Installation auf den Geräten erhalten werden. Es gibt folgende Konnektivitätsoptionen:
- Konnektivität. Die Verfügbarkeit von Konnektivität ist optimal, weil dadurch der Prozess einer lokalen Generierung von Zertifikaten optimiert wird. 
- Keine Konnektivität. In diesem Fall verwenden Sie ein signiertes Zertifikat einer Zertifizierungsstelle, um Gerätezertifikate lokal und offline zu generieren. 
- Keine Konnektivität. In diesem Fall können Sie Zertifikate abrufen, die im Voraus generiert wurden. Oder Sie können Zertifikate mithilfe einer Offline-PKI lokal generieren.

#### <a name="audit-requirement"></a>Überwachungsanforderung
Abhängig vom Typ der Geräte, die Sie herstellen, gibt es möglicherweise eine gesetzliche Anforderung zum Erstellen eines Überwachungspfads für die Installationsart von Geräteidentitäten auf Ihren Geräten. Durch die Überwachung erhöhen sich die Produktionskosten erheblich. In den meisten Fällen sollten Sie dies deshalb nur bei Bedarf tun. Wenn Sie nicht genau wissen, ob eine Überprüfung erforderlich ist, wenden Sie sich an die Rechtsabteilung Ihres Unternehmens. Es gibt folgende Überwachungsoptionen: 
- Keine sensible Branche. Eine Überwachung ist nicht erforderlich.
- Sensible Branche. Zertifikate sollten entsprechend den Anforderungen für Konformitätszertifizierung in einem sicheren Raum installiert werden. Wenn Sie einen sicheren Raum zum Installieren von Zertifikaten benötigen, wissen Sie wahrscheinlich bereits, wie Zertifikate in Ihren Geräten installiert werden. Und es gibt wahrscheinlich bereits ein Überwachungssystem. 

#### <a name="length-of-certificate-validity"></a>Gültigkeitsdauer des Zertifikats
Wie ein Führerschein haben Zertifikate ein Ablaufdatum, das bei ihrer Erstellung festgelegt wird. Dies sind die Optionen für die Gültigkeitsdauer eines Zertifikats:
- Eine Verlängerung ist nicht erforderlich.  Weil bei diesem Ansatz ein langer Verlängerungszeitraum verwendet wird, müssen Sie das Zertifikat während der Lebensdauer des Geräts nicht verlängern. Ein solcher Ansatz ist zwar praktisch, aber auch riskant.  Sie können das Risiko verringern, indem Sie auf Ihren Geräten einen sicheren Speicher wie ein HSM verwenden. Es wird allerdings empfohlen, die Verwendung langlebiger Zertifikate zu vermeiden.
- Eine Verlängerung ist erforderlich.  Sie müssen das Zertifikat während der Lebensdauer des Geräts verlängern. Die Gültigkeitsdauer des Zertifikats ist abhängig vom Kontext, und Sie benötigen eine Strategie für die Verlängerung.  In der Strategie sollte der Ort, von dem Sie Zertifikate erhalten, sowie die Art von drahtlosen Funktionen enthalten sein, die Ihre Geräte beim Verlängerungsvorgang verwenden müssen. 

### <a name="when-to-generate-certificates"></a>Zeitpunkt zum Generieren von Zertifikaten
Die Internetkonnektivitätsfunktionen in Ihrer Factory wirken sich auf Ihren Prozess zum Generieren von Zertifikaten aus. Sie haben mehrere Optionen für den Zeitpunkt zum Generieren von Zertifikaten: 

- Vorab geladene Zertifikate.  Einige HSM-Anbieter bieten einen Premium-Dienst, bei dem der Anbieter Zertifikate für den Kunden installiert. Zuerst gestatten Kunden dem HSM-Anbieter Zugriff auf ein Signaturzertifikat. Dann installiert der HSM-Anbieter Zertifikate, die durch dieses Signaturzertifikat signiert wurden, auf jedem vom Kunden erworbenen HSM. Der Kunde muss lediglich das HSM auf dem Gerät installieren. Dieser Dienst erhöht zwar die Kosten, hilft aber bei der Optimierung Ihres Fertigungsprozesses.  Und er beantwortet die Frage, wann Zertifikate installiert werden müssen.
- Vom Gerät generierte Zertifikate.  Wenn Ihre Geräte Zertifikate intern generieren, müssen Sie das öffentliche X.509-Zertifikat aus dem Gerät extrahieren, um es in DPS zu registrieren. 
- Verbundene Factory.  Wenn Ihre Factory über Konnektivität verfügt, können Sie Gerätezertifikate bei Bedarf jederzeit generieren.
- Offline-Factory mit Ihrer eigenen PKI. Wenn Ihre Factory nicht über Konnektivität verfügt und Sie Ihre eigene PKI mit Offlineunterstützung verwenden, können Sie die Zertifikate bei Bedarf generieren.
- Offline-Factory mit Drittanbieter-PKI. Wenn Ihre Factory nicht über Konnektivität verfügt und Sie eine Drittanbieter-PKI verwenden, müssen Sie die Zertifikate vorab generieren. Außerdem müssen die Zertifikate von einem Ort aus generiert werden, der über Konnektivität verfügt. 

### <a name="when-to-install-certificates"></a>Zeitpunkt zum Installieren von Zertifikaten
Nachdem Sie Zertifikate für Ihre IoT-Geräte generiert haben, können Sie sie in den Geräten installieren. 

Wenn Sie vorab geladene Zertifikate bei einem HSM verwenden, wird der Prozess vereinfacht. Nachdem das HSM im Gerät installiert wurde, kann der Gerätecode darauf zugreifen. Anschließend rufen Sie die HSM-APIs auf, um auf das im HSM gespeicherte Zertifikat zuzugreifen. Dieser Ansatz ist für Ihren Fertigungsprozess am besten geeignet. 

Wenn Sie kein vorab geladenes Zertifikat verwenden, müssen Sie das Zertifikat im Rahmen ihres Produktionsprozesses installieren. Der einfachste Ansatz besteht darin, das Zertifikat im HSM zu demselben Zeitpunkt zu installieren, zu dem Sie das erste Firmwareimage einspielen. Ihr Prozess muss einen Schritt hinzufügen, um das Image auf jedem Gerät zu installieren. Nach diesem Schritt können Sie abschließende Qualitätsprüfungen und alle anderen Schritte ausführen, bevor Sie das Gerät verpacken und versenden. 

Es sind Softwaretools verfügbar, mit denen Sie den Installationsprozess und die abschließende Qualitätsprüfung in einem einzigen Schritt ausführen können. Sie können diese Tools ändern, um ein Zertifikat zu generieren oder ein Zertifikat aus einem vorab generierten Zertifikatspeicher per Pull abzurufen. Anschließend kann die Software das Zertifikat dort installieren, wo Sie es installieren müssen. Softwaretools dieses Typs ermöglichen Ihnen eine Fertigung von Produktionsqualität im großen Stil. 

Nachdem Sie Zertifikate auf Ihren Geräten installiert haben, erfahren Sie im nächsten Schritt, wie Sie die Geräte beim [DPS](about-iot-dps.md) registrieren. 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrieren eines TPM in den Fertigungsprozess
Wenn Sie ein TPM zum Authentifizieren Ihrer IoT-Geräte verwenden, finden Sie in diesem Abschnitt entsprechende Anleitungen. In diesem Leitfaden werden die häufig verwendeten TPM 2.0-Geräte behandelt, die Unterstützung für HMAC-Schlüssel (Hash-based Message Authentication Code) bieten. Die TPM-Spezifikation für TPM-Chips ist ein ISO-Standard, der von der Trusted Computing Group verwaltet wird. Weitere Informationen zu TPM finden Sie in den Spezifikationen für [TPM 2.0](https://trustedcomputinggroup.org/tpm-library-specification/) und [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Besitzübernahme für das TPM
Ein wichtiger Schritt bei der Fertigung eines Geräts mit einem TPM-Chip ist die Besitzübernahme für das TPM. Dieser Schritt ist erforderlich, damit Sie dem Gerätebesitzer einen Schlüssel bereitstellen können. Der erste Schritt ist das Extrahieren des Endorsement Keys (EK) aus dem Gerät. Der nächste Schritt ist die Beanspruchung des Besitzes. Die dafür erforderliche Vorgehensweise ist davon abhängig, welches TPM und welches Betriebssystem Sie verwenden. Wenden Sie sich bei Bedarf an den TPM-Hersteller oder den Entwickler des Gerätebetriebssystems, um zu bestimmen, wie der Besitz beansprucht werden soll. 

In Ihrem Fertigungsprozess können Sie den EK extrahieren und den Besitz zu verschiedenen Zeiten beanspruchen. Dies erhöht die Flexibilität. Viele Hersteller nutzen diese Flexibilität, indem sie ein Hardwaresicherheitsmodul (HSM) hinzufügen, um die Sicherheit ihrer Geräte zu verbessern. Dieser Abschnitt enthält Anleitungen dazu, wann der EK extrahiert und der Besitz des TPM beansprucht werden muss, sowie Überlegungen zum Integrieren dieser Schritte in eine Fertigungszeitachse. 

> [!IMPORTANT]
> In der folgenden Anleitung wird davon ausgegangen, dass Sie ein diskretes, integriertes oder Firmware-TPM verwenden. Dort, wo es zutrifft, sind der Anleitung Hinweise zur Verwendung eines nicht diskreten oder Software-TPMs hinzugefügt. Wenn Sie ein Software-TPM verwenden, gibt es möglicherweise weitere Schritte, die in dieser Anleitung nicht enthalten sind. Bei Software-TPMs gibt es eine Vielzahl von Implementierungen, die über den Rahmen dieses Artikels hinausgehen.  Generell ist es möglich, ein Software-TPM in die nachstehende allgemeine Fertigungszeitachse zu integrieren. Obwohl ein softwareemuliertes TPM für das Erstellen von Prototypen und Tests geeignet ist, kann es nicht dieselbe Sicherheitsstufe wie ein diskretes, integriertes oder Firmware-TPM bereitstellen. Vermeiden Sie generell die Verwendung eines Software-TPM in der Produktion.

### <a name="general-manufacturing-timeline"></a>Allgemeine Fertigungszeitachse
Die nachstehende Zeitachse zeigt, wie ein TPM einen Produktionsprozess durchläuft und in einem Gerät endet. Jeder Fertigungsprozess ist eindeutig, und diese Zeitachse zeigt die am häufigsten verwendeten Muster. Die Zeitachse bietet Anleitungen dazu, wann bestimmte Aktionen mit den Schlüsseln ausgeführt werden müssen. 

#### <a name="step-1-tpm-is-manufactured"></a>Schritt 1: Das TPM wird hergestellt.
- Wenn Sie TPMs von einem Hersteller zur Verwendung in Ihren Geräten erwerben, ermitteln Sie, ob sie öffentliche Endorsement Keys (EK_pubs) automatisch extrahieren. Es ist hilfreich, wenn der Hersteller die Liste der EK_pubs zusammen mit den gelieferten Geräten bereitstellt. 
    > [!NOTE]
    > Sie könnten dem TPM-Hersteller mithilfe von Shared Access-Richtlinien in Ihrem Bereitstellungsdienst Schreibzugriff auf Ihre Registrierungsliste erteilen.  Bei diesem Ansatz können die Richtlinien die TPMs der Registrierungsliste automatisch hinzufügen.  Weil dies aber früh im Fertigungsprozess geschieht, müssen Sie dafür dem TPM-Hersteller vertrauen. Tun Sie dies auf eigene Gefahr. 

- Wenn Sie TPMs zum Verkauf an Gerätehersteller fertigen, empfiehlt es sich, Ihren Kunden eine Liste der EK_pubs zusammen mit ihren physischen TPMs zu liefern.  Durch die Bereitstellung von EK_pubs für Kunden wird ein Schritt in deren Prozess eingespart. 
- Wenn Sie TPMs für die Verwendung bei Ihren eigenen Geräten fertigen, ermitteln Sie, an welchem Punkt in Ihrem Prozess der EK_pub am einfachsten zu extrahieren ist. Sie können den EK_pub an jedem beliebigen verbleibenden Punkt in der Zeitachse extrahieren. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Schritt 2: TPM wird in einem Gerät installiert.
An diesem Punkt im Fertigungsprozess sollten Sie wissen, bei welcher DPS-Instanz das Gerät verwendet wird. Daher können Sie der Registrierungsliste Geräte für automatisierte Bereitstellung hinzufügen. Weitere Informationen zur automatischen Gerätebereitstellung finden Sie in der [DPS-Dokumentation](about-iot-dps.md).
- Wenn Sie den EK_pub nicht extrahiert haben, ist dies jetzt ein guter Zeitpunkt dafür. 
- Je nach dem Installationsprozess des TPM ist dieser Schritt auch ein guter Zeitpunkt für die TPM-Besitzübernahme. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Schritt 3: Auf dem Gerät ist Firmware und Software installiert.
Installieren Sie an diesem Punkt im Prozess den DPS-Client zusammen mit dem ID-Bereich und der globalen URL für die Bereitstellung.
- Dies ist jetzt die letzte Möglichkeit zum Extrahieren des EK_pub. Wenn ein Drittanbieter die Software auf Ihrem Gerät installiert, empfiehlt es sich, zuerst den EK_pub zu extrahieren.
- Dieser Punkt im Fertigungsprozess ist zur TPM-Besitzübernahme ideal geeignet.  
    > [!NOTE]
    > Wenn Sie ein Software-TPM verwenden, können Sie es jetzt installieren.  Extrahieren Sie gleichzeitig den EK_pub.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Schritt 4: Das Gerät wird verpackt und ins Lagerhaus gesendet.
Ein Gerät kann manchmal bis zu ein Jahr eingelagert sein, bevor es mit DPS bereitgestellt wird.  Wenn sich ein Gerät vor der Bereitstellung lange in einem Lager befunden hat, müssen Kunden, die das Gerät bereitstellen, möglicherweise die Firmware, Software oder abgelaufene Anmeldeinformationen aktualisieren.

#### <a name="step-5-device-is-installed-into-the-location"></a>Schritt 5: Das Gerät wird am Standort installiert.
Nachdem das Gerät an seinem endgültigen Standort angekommen ist, durchläuft es die automatisierte Bereitstellung mit DPS.

Weitere Informationen finden Sie unter [Bereitstellung](about-iot-dps.md#provisioning-process) und [TPM-Nachweis](concepts-tpm-attestation.md). 

## <a name="resources"></a>Ressourcen

Zusätzlich zu den empfohlenen Sicherheitsmaßnahmen in diesem Artikel bietet Azure IoT Ressourcen, die Sie bei der Auswahl sicherer Hardware und der Erstellung sicherer IoT-Bereitstellungen unterstützen sollen: 
- Azure IoT-[Sicherheitsempfehlungen](../iot-fundamentals/security-recommendations.md) zur Unterstützung des Bereitstellungsprozesses. 
- [Microsoft Defender für Cloud](https://azure.microsoft.com/services/security-center/) bietet einen Dienst zum Erstellen sicherer IoT-Bereitstellungen. 
- Hilfe bei der Auswertung Ihrer Hardwareumgebung finden Sie im Whitepaper [Auswerten ihrer IoT-Sicherheit](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Hilfe bei der Auswahl von sicherer Hardware finden Sie unter [The Right Secure Hardware for your IoT Deployment](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf) (Die richtige sichere Hardware für Ihre IoT-Bereitstellung). 
