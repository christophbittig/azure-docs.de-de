---
title: 'Informationen zu Azure Key Vault-Geheimnissen: Azure Key Vault'
description: Übersicht über Azure Key Vault-Geheimnisse
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 54ebf914b27c8cd91272e4b8e5c8834e5331cdf4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057421"
---
# <a name="about-azure-key-vault-secrets"></a>Informationen zu Azure Key Vault-Geheimnissen

[Key Vault](../general/overview.md) bietet einen sicheren Speicher für generische Geheimnisse wie z. B. Kennwörter und Datenbankverbindungszeichenfolgen.

Aus Entwicklerperspektive akzeptieren Key Vault-APIs geheime Werte als Zeichenfolge und geben sie auch in dieser Form zurück. Intern speichert und verwaltet Key Vault Geheimnisse als Oktettsequenzen (8-Bit-Bytes), wobei jede Sequenz maximal 25.000 Bytes groß sein darf. Der Key Vault-Dienst bietet keine Semantik für Geheimnisse. Der Dienst akzeptiert nur die Daten, verschlüsselt sie, speichert sie und gibt einen Bezeichner (ID) für das Geheimnis zurück. Der Bezeichner kann dazu verwendet werden, das Geheimnis zu einem späteren Zeitpunkt abzurufen.  

Bei hochvertraulichen Daten sollten zusätzliche Schutzebenen für die Daten eingerichtet werden. Die Verschlüsselung von Daten mithilfe eines separaten Schutzschlüssels vor dem Speichern in Key Vault ist nur ein Beispiel hierfür.  

Key Vault unterstützt auch ein contentType-Feld für Geheimnisse. Clients können den Inhaltstyp eines Geheimnisses als Hilfestellung beim Interpretieren der geheimen Daten beim Abrufen angeben. Dieses Feld darf maximal 255 Zeichen lang sein. Die Verwendungsempfehlung dient als Hinweis für das Interpretieren der geheimen Daten. Wenn eine Implementierung z.B. sowohl Kennwörter als auch Zertifikate als Geheimnisse speichert, verwenden Sie dieses Feld zur Unterscheidung. Es gibt keine vordefinierten Werte.  

## <a name="encryption"></a>Verschlüsselung

Alle Geheimnisse in Ihrer Key Vault-Instanz werden verschlüsselt gespeichert. Key Vault verschlüsselt Geheimnisse im Ruhezustand mithilfe einer Hierarchie von Verschlüsselungsschlüsseln, wobei alle Schlüssel in dieser Hierarchie durch Module geschützt sind, die FIPS 140-2-konform sind. Diese Verschlüsselung ist transparent, und der Benutzer muss keine Aktion durchführen. Ihre Geheimnisse werden vom Azure Key Vault-Dienst verschlüsselt, wenn Sie diese hinzufügen, und dann automatisch entschlüsselt, wenn Sie sie lesen.

Der Verschlüsselungsblattschlüssel der Schlüsselhierarchie ist für jeden Schlüsseltresor eindeutig. Der Verschlüsselungsstammschlüssel der Schlüsselhierarchie ist in der Sicherheitsumgebung eindeutig, und seine Schutzebene variiert je nach Region:
- China: Der Stammschlüssel ist durch ein Modul geschützt, das gemäß FIPS 140-2 Level 1 validiert ist. 
- Andere Regionen: Der Stammschlüssel ist durch ein Modul geschützt, das gemäß FIPS 140-2 Level 2 oder höher validiert ist. 

## <a name="secret-attributes"></a>Geheimnisattribute

Zusätzlich zu den Geheimnisdaten können die folgenden Attribute angegeben werden:  

- *exp*: IntDate, optional, Standardwert ist **forever**. Das *exp*-Attribut (Ablaufzeit) gibt die Ablaufzeit an, nach deren Verstreichen die geheimen Daten – außer in [bestimmten Situationen](#date-time-controlled-operations) – NICHT abgerufen werden sollen. Dieses Feld dient nur zu **Informationszwecken**, da es die Benutzer des Azure Key Vault-Diensts darüber informiert, dass ein bestimmter geheimer Schlüssel möglicherweise nicht verwendet werden kann. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält.   
- *nbf*: IntDate, optional, Standardwert ist **now**. Das *nbf*-Attribut („not before“, nicht vor) gibt den Zeitpunkt an, vor dem die geheimen Daten – außer in [bestimmten Situationen](#date-time-controlled-operations) – NICHT abgerufen werden sollen. Dieses Feld dient nur zu **Informationszwecken**. Der Wert MUSS eine Zahl sein, die einen IntDate-Wert enthält. 
- *enabled*: Boolesch, optional, Standardwert ist **true**. Dieses Attribut gibt an, ob die Geheimnisdaten abgerufen werden können. Das enabled-Attribut wird in Verbindung mit *nbf* und *exp* verwendet. Wenn ein Vorgang zwischen *nbf* und *exp* auftritt, wird er nur zugelassen, wenn „enabled“ auf **true** festgelegt ist. Vorgänge außerhalb des *nbf*- und *exp*-Fensters sind automatisch unzulässig, [bestimmte Situationen](#date-time-controlled-operations) ausgenommen.  

Es gibt zusätzliche schreibgeschützte Attribute, die in alle Antworten einbezogen werden, die Geheimnisattribute enthalten:  

- *created*: IntDate, optional. Das „created“-Attribut gibt an, wann diese Version des Geheimnisses erstellt wurde. Dieser Wert ist NULL für Geheimnisse, die vor dem Hinzufügen dieses Attributs erstellt wurden. Der Wert muss eine Zahl sein, die einen IntDate-Wert enthält.  
- *updated*: IntDate, optional. Das updated-Attribut gibt an, wann diese Version des Geheimnisses aktualisiert wurde. Dieser Wert ist NULL für Geheimnisse, die vor dem Hinzufügen dieses Attributs zuletzt aktualisiert wurden. Der Wert muss eine Zahl sein, die einen IntDate-Wert enthält.

Informationen zu allgemeinen Attributen für die einzelnen Key Vault-Objekttyp finden Sie unter [Übersicht über Schlüssel, Geheimnisse und Zertifikate in Azure Key Vault](../general/about-keys-secrets-certificates.md).

### <a name="date-time-controlled-operations"></a>Durch Datum und Uhrzeit gesteuerte Vorgänge

Der **get**-Geheimnisvorgang funktioniert für noch nicht gültige und abgelaufene Geheimnisse außerhalb des *nbf* / *exp*-Fensters. Das Aufrufen eines **get**-Geheimnisvorgangs für ein noch nicht gültiges Geheimnis ist zu Testzwecken möglich. Abrufvorgänge (per **get**) für ein abgelaufenes Geheimnis können für Wiederherstellungsvorgänge verwendet werden.

## <a name="secret-access-control"></a>Geheimniszugriffssteuerung

Die Zugriffssteuerung für Geheimnisse, die in Key Vault verwaltet werden, wird auf der Ebene der Key Vault-Instanz bereitgestellt, die diese Geheimnisse enthält. Die Zugriffssteuerungsrichtlinie für Geheimnisse unterscheidet sich von der Zugriffssteuerungsrichtlinie für Schlüssel im selben Key Vault. Benutzer können einen oder mehrere Tresore zum Speichern von Geheimnissen erstellen und müssen für eine dem Szenario entsprechende Segmentierung und Verwaltung von Geheimnissen sorgen.   

Die folgenden Berechtigungen können pro Prinzipal im Zugriffssteuerungseintrag für Geheimnisse in einem Tresor verwendet werden und spiegeln die für ein Geheimnisobjekt zulässigen Vorgänge präzise wider:  

- Berechtigungen für Geheimnisverwaltungsvorgänge
  - *get*: Lesen eines Geheimnisses  
  - *list*: Auflisten der Geheimnisse oder Versionen eines in einem Schlüsseltresor gespeicherten Geheimnisses  
  - *set*: Erstellen eines Geheimnisses  
  - *delete*: Löschen eines Geheimnisses  
  - *recover*: Wiederherstellen eines gelöschten Geheimnisses
  - *backup*: Sichern eines Geheimnisses in einem Schlüsseltresor
  - *restore*: Wiederherstellen eines gesicherten Geheimnisses in einem Schlüsseltresor

- Berechtigungen für privilegierte Vorgänge
  - *purge*: Bereinigen (dauerhaftes Löschen) eines gelöschten Geheimnisses

Weitere Informationen zur Verwendung von Geheimnissen finden Sie unter den [Geheimnisvorgängen in der Referenz zur REST-API für Azure Key Vault](/rest/api/keyvault). Informationen zum Einrichten von Berechtigungen finden Sie unter [Tresore – Erstellen oder Aktualisieren](/rest/api/keyvault/vaults/createorupdate) und [Vaults – Aktualisieren der Zugriffsrichtlinie](/rest/api/keyvault/vaults/updateaccesspolicy). 

Schrittanleitungen zum Steuern des Zugriffs in Key Vault:
- [Zuweisen einer Key Vault-Zugriffsrichtlinie mithilfe der Befehlszeilenschnittstelle](../general/assign-access-policy-cli.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie mithilfe von PowerShell](../general/assign-access-policy-powershell.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie über das Azure-Portal](../general/assign-access-policy-portal.md)
- [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../general/rbac-guide.md)

## <a name="secret-tags"></a>Geheimnistags  
Sie können zusätzliche anwendungsspezifische Metadaten in Form von Tags angeben. Key Vault unterstützt bis zu 15 Tags, von denen jedes einen 256 Zeichen langen Namen und einen Wert von 256 Zeichen aufweisen kann.  

>[!Note]
>Tags sind für Aufrufer lesbar, die über die *list*- oder *get*-Berechtigung verfügen.

## <a name="usage-scenarios"></a>Verwendungsszenarien

| Verwendung | Beispiele |
|--------------|-------------|
|Sicheres Speichern, Verwalten des Lebenszyklus und Überwachen von Anmeldeinformationen (z. B. Kennwörter, Zugriffsschlüssel und geheime Clientschlüssel für Dienstprinzipale) für die Kommunikation zwischen Diensten.  | - [Verwenden von Azure Key Vault mit einem virtuellen Computer](../general/tutorial-net-virtual-machine.md)<br> - [Verwenden von Azure Key Vault mit einer Azure-Web-App](../general/tutorial-net-create-vault-azure-web-app.md) |

## <a name="next-steps"></a>Nächste Schritte

- [Best Practices für die Geheimnisverwaltung in Key Vault](secrets-best-practices.md)
- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../general/about-keys-secrets-certificates.md)
- [Zuweisen einer Key Vault-Zugriffsrichtlinie](../general/assign-access-policy.md)
- [Gewähren des Zugriffs auf Key Vault-Schlüssel, -Zertifikate und -Geheimnisse mit der rollenbasierten Zugriffssteuerung in Azure](../general/rbac-guide.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/security-features.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
