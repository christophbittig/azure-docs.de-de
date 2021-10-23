---
title: Problembehandlung beim Hive-Connector
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme beim Hive-Connector in Azure Data Factory und Azure Synapse Analytics behandeln können.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8412d5d49a0e457b42fce408440693fa5ffc6d4c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067452"
---
# <a name="troubleshoot-the-hive-connector-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung beim Hive-Connector in Azure Data Factory und Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme beim Hive-Connector in Azure Data Factory und Azure Synapse.

## <a name="the-performance-difference-between-the-odbc-connector-and-the-hive-connector"></a>Der Leistungsunterschied zwischen dem ODBC-Connector und dem Hive-Connector

- **Symptome** Die Bedenken im Hinblick auf den Leistungsunterschied zwischen dem ODBC-Connector und dem Hive-Connector. 

- **Ursache** Der ODBC-Connector benötigt einen eigenen Treiber, und das kann aufgrund der Treiberqualität von Drittanbietern zu Leistungsunterschieden führen.

- **Empfehlung**: Verwenden Sie zuerst den Hive-Connector. 


## <a name="unexpected-response-received-from-the-server-when-connecting-to-the-hive-server-via-odbc"></a>Unerwartete Antwort vom Server beim Herstellen einer Verbindung mit dem Hive-Server über ODBC

- **Symptome**: Beim Herstellen einer Verbindung mit dem Hive-Server mithilfe des mit ODBC verknüpften Diensts wird diese Fehlermeldung angezeigt: `ERROR [HY000] [Cloudera][DriverSupport] (1110) Unexpected response received from server. Please ensure the server host and port specified for the connection are correct and confirm if SSL should be enabled for the connection.`

- **Ursache**: Sie verwenden die Kerberos-Authentifizierung, die in Azure Data Factory nicht unterstützt wird.

- **Empfehlung**: Probieren Sie die folgenden Schritte aus. Wenn sie nicht funktionieren, überprüfen Sie den bereitgestellten Treiber, um dieses Problem zu beheben.
    1. Die Datei **kr5.ini** ist im Ordner **C:\Programme\MIT\Kerberos\bin** gespeichert.
    2. Fügen Sie `KRB5_CONFIG` und `KRB5CCNAME` auch der Systemvariablen hinzu.
    3. Bearbeiten Sie die Datei **krb.ini**.
    4. Fahren Sie den virtuellen Computer herunter, und starten Sie ihn und die selbstgehostete Integration Runtime (Self-Hosted Integration Runtime, SHIR) vom Computer aus neu.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)