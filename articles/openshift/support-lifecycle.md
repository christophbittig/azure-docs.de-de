---
title: Supportlebenszyklus für Azure Red Hat OpenShift
description: Grundlegendes zum Supportlebenszyklus und den unterstützten Versionen für Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/16/2021
ms.openlocfilehash: 2c1eeb97fecac449e85aa0a5d1987dc6ef2c4b4f
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614268"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Supportlebenszyklus für Azure Red Hat OpenShift 4

Red Hat veröffentlicht etwa alle drei Monate Nebenversionen von Red Hat OpenShift Container Platform (OCP). Diese Releases enthalten neue Features und Verbesserungen. Patchreleases kommen häufiger vor (normalerweise wöchentlich) und sind nur für wichtige Fehlerbehebungen innerhalb einer Nebenversion gedacht. Diese Patchreleases können Korrekturen für Sicherheitsrisiken oder schwer wiegende Fehler enthalten.

Azure Red Hat OpenShift basiert auf bestimmten Releases von OCP. In diesem Artikel werden die OCP-Versionen beschrieben, die für Azure Red Hat OpenShift unterstützt werden. Außerdem sind Details zu Upgrades, veralteten Versionen und der Supportrichtlinie enthalten.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift-Versionen

Red Hat OpenShift Container Platform verwendet die semantische Versionsverwaltung. Bei der semantischen Versionsverwaltung werden verschiedene Ebenen von Versionsnummern zur Angabe verschiedener Ebenen der Versionsverwaltung verwendet. In der folgenden Tabelle werden die verschiedenen Teile einer semantischen Versionsnummer am Beispiel der Versionsnummer 4.4.11 veranschaulicht.

|Hauptversion (x)|Nebenversion (y)|Patch (z)|
|-|-|-|
|4|4|11|

Für jede Zahl in der Version gilt, dass allgemeine Kompatibilität mit der vorherigen Version besteht:

* **Hauptversion**: Derzeit sind keine Hauptversionen geplant. Die Hauptversionen ändern sich, wenn nicht kompatible API-Änderungen vorgenommen werden oder möglicherweise keine Abwärtskompatibilität mehr gewährleistet wird.
* **Nebenversion**: Wird etwa alle drei Monate veröffentlicht. Nebenversionsupgrades können Zusatzfunktionen, Erweiterungen, Veraltungen, Entfernungen, Fehlerbehebungen, Sicherheitserweiterungen und andere Verbesserungen enthalten.
* **Patches**: Werden normalerweise wöchentlich oder nach Bedarf veröffentlicht. Patchversionsupgrades können Fehlerbehebungen, Sicherheitserweiterungen und andere Verbesserungen enthalten.

Kunden sollten nach Möglichkeit immer das neueste Nebenrelease der aktuell verwendeten Hauptversion nutzen. Wenn Ihr Produktionscluster beispielsweise die Version 4.4 verwendet und 4.5 die neueste allgemein verfügbare Nebenversion für die 4er Reihe ist, sollten Sie so bald wie möglich ein Upgrade auf Version 4.5 durchführen. 

### <a name="upgrade-channels"></a>Upgradekanäle

Upgradekanäle sind an eine Nebenversion von Red Hat OpenShift Container Platform (OCP) gebunden. Beispielsweise enthalten Upgradekanäle für OCP 4.4 niemals ein Upgrade auf ein 4.5-Release. Upgradekanäle steuern lediglich die Releaseauswahl und haben keine Auswirkung auf die Version des Clusters.

Azure Red Hat OpenShift 4 unterstützt nur stabile Kanäle, z. B. „stable-4.4“.

Über den Kanal „stable-4.5“ können Sie ein Upgrade von einer früheren Nebenversion von Azure Red Hat OpenShift durchführen. Cluster, die über schnelle Kanäle, Kanäle für Vorabversionen und Kandidatenkanälen aktualisiert werden, werden nicht unterstützt.

Wenn Sie zu einem Kanal wechseln, der Ihr aktuelles Release nicht enthält, wird eine Warnung angezeigt, und es können keine Updates empfohlen werden. Sie können jedoch jederzeit sicher zum ursprünglichen Kanal zurückwechseln.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Richtlinie zur Unterstützung von Red Hat OpenShift Container Platform-Versionen

Azure Red Hat OpenShift unterstützt zwei allgemein verfügbare Nebenversionen von Red Hat OpenShift Container Platform:
* Die neueste allgemein verfügbare Nebenversion, die in Azure Red Hat OpenShift veröffentlicht wurde (diese wird als N bezeichnet)
* Eine vorherige Nebenversion (N-1)

Bei Bereitstellung in einem stabilen Upgradekanal können neuere Nebenreleases (N+1, N+2), die in Upstream-OCP verfügbar sind, ebenfalls unterstützt werden.

Kritische Patchupdates werden von Azure Red Hat OpenShift Site Reliability Engineers (SRE) automatisch auf Cluster angewendet. Kunden, die Patchupdates vorab installieren möchten, können dies tun.

Wenn Azure Red Hat OpenShift beispielsweise die Version 4.5.z einführt, werden die folgenden Versionen unterstützt:

|Neue Nebenversion|Liste der unterstützten Versionen|
|-|-|
|4.5.z|4.5.z, 4.4.z|

Hierbei steht „.z“ für die jeweiligen Patchversionen. Wenn die Version in einem stabilen Upgradekanal verfügbar ist, können Kunden auch ein Upgrade auf 4.6.z durchführen.

Bei Einführung einer neuen Nebenversion wird die älteste Nebenversion als veraltet gekennzeichnet und entfernt. Angenommen, die aktuelle Liste der unterstützten Versionen umfasst 4.5.z und 4.4.z. Wenn Azure Red Hat OpenShift die Version 4.6.z veröffentlicht, wird Version 4.4.z entfernt und innerhalb von 30 Tagen nicht mehr unterstützt.

> [!NOTE]
> Wenn Kunden eine nicht unterstützte Red Hat OpenShift-Version verwenden, werden sie möglicherweise aufgefordert, ein Upgrade vorzunehmen, sobald sie Support für den Cluster anfordern. Die Azure Red Hat OpenShift SLA gilt nicht für Cluster, auf denen nicht unterstützte Red Hat OpenShift-Releases ausgeführt werden.

## <a name="release-and-deprecation-process"></a>Veröffentlichen und Markieren als veraltet

Sie können neue Releases und veraltete Versionen im Releasekalender für Azure Red Hat OpenShift einsehen.

Bei neuen Nebenversionen von Red Hat OpenShift Container Platform gilt Folgendes:
* Das SRE-Team für Azure Red Hat OpenShift veröffentlicht mindestens 30 Tage vor dem Entfernen einer Version in den [Versionshinweisen für Azure Red Hat OpenShift](https://github.com/Azure/OpenShift/releases) eine Vorankündigung mit dem geplanten Datum der neuen Version und der Entfernung der entsprechenden alten Version.
* Das SRE-Team für Azure Red Hat OpenShift veröffentlicht eine Benachrichtigung zur Dienstintegrität, die für alle Kunden mit Zugriff auf Azure Red Hat OpenShift und das Portal verfügbar ist. Außerdem sendet das SRE-Team eine E-Mail an die Abonnementadministratoren mit den geplanten Daten für die Entfernung von Versionen.
* Kunden haben nach der Entfernung einer Version 30 Tage lang Zeit, ein Upgrade auf eine unterstützte Nebenversion durchzuführen, um sicherzustellen, dass sie weiterhin Support erhalten.

Bei neuen Patchversionen von Red Hat OpenShift Container Platform gilt Folgendes:
* Aufgrund der dringenden Natur von Patchversionen können diese vom SRE-Team für Azure Red Hat OpenShift in den Dienst eingefügt werden, sobald sie verfügbar sind.
* Im Allgemeinen nimmt das SRE-Team für Azure Red Hat OpenShift keine umfassenden Mitteilungen bei der Installation neuer Patchversionen vor. Das Team überwacht und überprüft allerdings verfügbare CVE-Patches, um diese möglichst schnell zu unterstützen. Wenn eine Kundenaktion erforderlich ist, benachrichtigt das Team Kunden über das Upgrade.

## <a name="supported-versions-policy-exceptions"></a>Richtlinienausnahmen für unterstützte Versionen

Das SRE-Team für Azure Red Hat OpenShift behält sich das Recht vor, ohne Vorankündigung neue oder bestehende Versionen hinzuzufügen oder zu entfernen oder anstehende Nebenversionen zu verzögern, wenn in diesen kritische, die Produktion einschränkende Fehler oder Sicherheitsprobleme gefunden werden.

Bestimmte Patchreleases können übersprungen oder Rollouts je nach Schweregrad des Fehlers oder Sicherheitsproblems beschleunigt werden.

## <a name="azure-portal-and-cli-versions"></a>Versionen für Azure-Portal and CLI

Wenn Sie einen Azure Red Hat OpenShift-Cluster im Portal oder mit der Azure CLI bereitstellen, wird der Cluster standardmäßig auf die neueste Nebenversion (N) und den neuesten kritischen Patch festgelegt. Wenn Azure Red Hat OpenShift beispielsweise 4.5.z und 4.4.z unterstützt, ist die Standardversion für Neuinstallationen 4.5.z. Kunden, die die neueste Nebenversion von Upstream-OCP (N+1, N+2) verwenden möchten, können ihren Cluster jederzeit auf ein beliebiges Release aktualisieren, das in den stabilen Upgradekanälen verfügbar ist.

## <a name="azure-red-hat-openshift-release-calendar"></a>Releasekalender für Azure Red Hat OpenShift

Im folgenden Leitfaden finden Sie den [bisherigen Releaseverlauf für Red Hat OpenShift Container Platform (Upstream)](https://access.redhat.com/support/policy/updates/openshift/#dates).

|OCP-Version|Upstreamrelease|Allgemeine Verfügbarkeit von Azure Red Hat OpenShift|Ende der Lebensdauer|
|-|-|-|-|
|4.4|Mai 2020|Juli 2020|4.6 GA|
|4,5|Juli 2020| November 2020|4.7 GA|
|4.6|Oktober 2020| Februar 2021|4.8 GA|
|4,7|Februar 2021| 15. Juli 2021|4.9 Allgemeine Verfügbarkeit|
|4.8|Juli 2021| 15. September 2021|4.10 GA|

## <a name="faq"></a>Häufig gestellte Fragen

**Was geschieht, wenn ein Benutzer ein Upgrade eines OpenShift-Clusters mit einer Nebenversion durchführt, die nicht unterstützt wird?**

Wenn Sie die Version N-2 oder älter verwenden, wird diese Version nicht mehr unterstützt, und Sie werden zu einem Upgrade aufgefordert, damit der Support weiterhin besteht. Wenn das Upgrade von Version N-2 auf N-1 erfolgreich ist, greift der Support wieder. Das Upgrade von Version N-3 oder früher auf eine unterstützte Version kann schwierig und in einigen Fällen gar nicht möglich sein. Es wird empfohlen, ihren Cluster mit neuesten OpenShift-Version zu betreiben, um potenzielle Upgradeprobleme zu vermeiden. Beispiel:
* Wenn die älteste unterstützte Azure Red Hat OpenShift-Version 4.4.z ist und Sie 4.3.z oder eine ältere Version verwenden, steht Ihnen kein Support zur Verfügung.
* Wenn das Upgrade von 4.3.z auf 4.4.z oder höher erfolgreich ist, befinden Sie sich wieder im Rahmen unserer Supportrichtlinien. 

Das Zurücksetzen des Clusters auf eine frühere Version oder ein Rollback wird nicht unterstützt. Nur das Upgrade auf eine neuere Version wird unterstützt.

**Was bedeutet „nicht unterstützt“?**

Wenn ihr ARO-Cluster eine OpenShift-Version ausführt, die nicht in der Liste der unterstützten Versionen enthalten ist oder eine [nicht unterstützte Clusterkonfiguration](./support-policies-v4.md) verwendet, befindet sich Ihr Cluster „außerhalb des Supports“. Infolgedessen:
- Wenn Sie ein Supportticket für Ihren Cluster öffnen, werden Sie aufgefordert, den Cluster auf eine unterstützte Version zu aktualisieren. bevor Sie Support erhalten, es sei denn, Sie befinden sich innerhalb der 30-tägigen Toleranzperiode nach Ende des Versionssupports. 
- Alle Laufzeit- oder SLA-Garantien für Cluster außerhalb des Supports werden aufgehoben.
- Cluster außerhalb des Supports werden nur nach bestem Bemühen gepatcht.
- Cluster außerhalb des Supports werden nicht überwacht.
