---
title: Behandeln von Problemen bei der Änderung von Vorgesetzten in der Personalbereitstellung
description: Erfahren Sie, wie Sie Probleme bei der Änderung von Vorgesetzten in der Personalbereitstellung behandeln.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: d67e528420b9907949202c1f597f933a74f7c801
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324553"
---
# <a name="troubleshoot-hr-manager-update-issues"></a>Problembehandlung bei Personaländerungen für Vorgesetzte

**Anwendungsbereich:**
* Lokale Benutzerbereitstellung von Workday in Active Directory
* Benutzerbereitstellung von Workday in Azure Active Directory
* SAP SuccessFactors zur Benutzerbereitstellung in lokalem Active Directory
* Benutzerbereitstellung von SAP SuccessFactors in Azure Active Directory

## <a name="understanding-how-manager-reference-resolution-works"></a>Grundlegendes zur Funktionsweise der Hierarchieauflösung
Der Azure AD-Bereitstellungsdienst aktualisiert automatisch Informationen zu Vorgesetzten, sodass die Benutzer-Vorgesetzten-Beziehung in Azure AD immer mit Ihren Personaldaten synchron ist. Dazu wird ein Prozess namens *Hierarchieauflösung* verwendet, um das *manager*-Attribut richtig zu aktualisieren. Bevor Sie sich intensiver mit den Prozessdetails beschäftigen, sollten Sie verstehen, wie Informationen zu Vorgesetzten in Azure AD und in einem lokalen Active Directory gespeichert werden. 

* In einer **lokalen Active Directory-Instanz** werden im *manager*-Attribut der *distinguishedName (dn)* des Kontos der oder des Vorgesetzten in AD gespeichert. 
* In **Azure AD** ist das *manager*-Attribut eine DirectoryObject-Navigationseigenschaft in Azure AD. Wenn Sie den Benutzerdatensatz im Azure-Portal anzeigen, wird der *displayName* des Vorgesetztendatensatzes in Azure AD angezeigt. 

Die *Hierarchieauflösung* erfolgt in zwei Schritten: 
* Schritt 1: Verknüpfen Sie den Quelldatensatz von Vorgesetzten im Personalsystem mit dem Datensatz des Zielkontos der oder des Vorgesetzten, indem Sie ein Attributpaar verwenden, das als *Quellanker* und *Zielanker* bezeichnet wird. 
* Schritt 2: Verwenden Sie die im Schema definierten Verweisattribute für Vorgesetzte, um das manager-Attribut im Ziel im erforderlichen Format zu aktualisieren. 

Die Standardankerattribute und Verweisattribute für jede App sind unten aufgeführt. 

| App-Name | Ankerattribut | Verweisattribut im Benutzerprofil | 
|--|--|--| 
| Workday | WID | ManagerReference (verweist auf die WID des Vorgesetztendatensatzes) |
| SAP SuccessFactors | personIdExternal | manager (verweist auf die personIdExternal des Vorgesetztendatensatzes) |
| Lokales Active Directory | objectGUID | manager (verweist auf den DN des Vorgesetztendatensatzes) |
| Azure AD | objectId | manager (verweist auf den Azure AD-Datensatz der/des Vorgesetzten) |

## <a name="prerequisites-for-successful-manager-update"></a>Voraussetzungen für eine erfolgreiche Aktualisierung von Vorgesetzten
Damit die *Hierarchieauflösung* erfolgreich funktioniert, sollten die folgenden Voraussetzungen erfüllt sein: 
* Ihre Bereitstellungs-App sollte so konfiguriert sein, dass sie die oben aufgeführten Standardanker für Quelle und Ziel verwendet. Ändern Sie nicht die Metadateneigenschaften (Datentyp, API-Ausdruck), die diesen Anker- und Verweisattributen zugeordnet sind. 
* Die API-Ausdrücke (XPATH bei Workday und JSONPath bei SuccessFactors), die dem manager-Attribut zugeordnet sind, werden in einen gültigen Wert ungleich NULL aufgelöst. 
   * Workday-Standard-XPATH-API-Ausdruck für ManagerReference: `wd:Worker/wd:Worker_Data/wd:Management_Chain_Data/wd:Worker_Supervisory_Management_Chain_Data[position()=1]/wd:Management_Chain_Data[last()=position()]/wd:Manager_Reference/wd:ID[@wd:type='WID']/text()`
   * SuccessFactors-Standard-JSONPath-API-Ausdruck für „manager“: `$.employmentNav.results[0].userNav.manager.empInfo.personIdExternal`
* Der Vorgesetztendatensatz muss sich darüber hinaus im Bereich des Bereitstellungsauftrags befinden. 
* Die Bereitstellungs-App sollte den Vorgesetztendatensatz vor der Verarbeitung des Benutzerdatensatzes verarbeitet haben. 

## <a name="provision-on-demand-does-not-update-manager-attribute"></a>Bei einer bedarfsgesteuerten Bereitstellung wird das manager-Attribut nicht aktualisiert.
| Problembehandlung | Details |
|--|--|
| **Problem** | Sie haben die App für die eingehende Bereitstellung erfolgreich konfiguriert. Sie testen die Synchronisierung mit der bedarfsgesteuerten Bereitstellung. Das manager-Attribut wird nicht aktualisiert, und Sie erhalten die Fehlermeldung *„Ungültiger Wert“* .  |
| **Ursache** | Ihr Bereitstellungsauftrag erfüllt keine der [Voraussetzungen für eine erfolgreiche Aktualisierung von Vorgesetzten](#prerequisites-for-successful-manager-update).  |
| **Auflösung** | * Wenn Sie die Zuordnung für das manager-Attribut geändert haben, stellen Sie die Standardzuordnung wieder her. <br> * Stellen Sie sicher, dass sich der Vorgesetztendatensatz im Bereich befindet und der manager-API-Ausdruck in einen gültigen Wert aufgelöst wird. <br> * Führen Sie zuerst die bedarfsgesteuerte Bereitstellung für den Vorgesetztendatensatz und erst danach die bedarfsgesteuerte Bereitstellung für den Benutzerdatensatz aus.  |

## <a name="full-sync-does-not-update-manager-attribute"></a>Bei der vollständigen Synchronisierung wird das manager-Attribut nicht aktualisiert.
| Problembehandlung | Details |
|--|--|
| **Problem** | Sie haben die App für die eingehende Bereitstellung erfolgreich konfiguriert. Sie verwenden einen Bereichsfilter, um nur bestimmte Personaldatensätze zu verarbeiten. Sie stellen fest, dass für einige Benutzer*innen keine Hierarchieauflösung erfolgt.  |
| **Ursache** | Wenn Sie Bereichsfilter verwenden, liegt der Vorgesetztendatensatz höchstwahrscheinlich nicht im Bereich.  |
| **Auflösung** | Aktualisieren Sie den Bereichsfilter, um den Vorgesetztendatensatz im Bereich hinzuzufügen.  |

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr zu Integrationsszenarien und Webdienstaufrufen für Azure AD und Workday.](workday-integration-reference.md)
* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](check-status-user-account-provisioning.md)
