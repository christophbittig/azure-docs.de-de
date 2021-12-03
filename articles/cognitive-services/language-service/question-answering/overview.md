---
title: Was ist „Fragen und Antworten“?
description: „Fragen und Antworten“ ist ein cloudbasierter NLP-Dienst (Natural Language Processing, Verarbeitung natürlicher Sprache) zur mühelosen Erstellung einer natürlichen Konversationsebene für Ihre Daten. Er kann verwendet werden, um für eine beliebige Eingabe in natürlicher Sprache die am besten geeignete Antwort aus Ihrer benutzerdefinierten Wissensdatenbank (Knowledge Base, KB) zu finden.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
keywords: QnA Maker, Chatbots mit wenig Code, Konversationen mit Mehrfachdurchläufen
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 8249fa4f276b7474740e2c8d882abc4e69c9487d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476488"
---
# <a name="what-is-question-answering"></a>Was ist „Fragen und Antworten“?

„Fragen und Antworten“ ermöglicht die cloudbasierte Verarbeitung natürlicher Sprache (NLP), um eine natürliche Konversationsebene für Ihre Daten erstellen zu können. Er wird dazu verwendet, für eine beliebige Eingabe die am besten geeignete Antwort aus Ihrer benutzerdefinierten Wissensdatenbank (Knowledge Base, KB) zu finden.

„Fragen und Antworten“ wird häufig mithilfe von Clientanwendungen für die Konversation erstellt, darunter Anwendungen für soziale Medien, Chatbots und sprachaktivierte Desktopanwendungen. Mehrere neue Features wurden hinzugefügt, einschließlich verbesserter Relevanz mithilfe eines Deep Learning-Bewerters, präziser Antworten und End-to-End-Unterstützung für Regionen.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* In den [Schnellstarts](./quickstart/sdk.md) finden Sie Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten.
* Die [Anleitungen](./how-to/manage-knowledge-base.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die [konzeptionellen Artikel](./concepts/precise-answering.md) enthalten ausführliche Erläuterungen der Funktionen und Features eines Diensts.
* [**Tutorials**](./tutorials/bot-service.md) sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird. 

## <a name="when-to-use-question-answering"></a>Wann sollte „Fragen und Antworten“ verwendet werden?

* **Vorhandensein von statischen Informationen:** Verwenden Sie „Fragen und Antworten“, wenn Ihre Antwort-Wissensdatenbank statische Informationen enthält. Diese Wissensdatenbank ist speziell auf Ihre Anforderungen zugeschnitten und wurde anhand von Dokumenten wie PDFs und URLs erstellt.
* **Bereitstellung der gleichen Antwort für eine Anforderung, eine Frage oder einen Befehl:** Wenn verschiedene Benutzer die gleiche Frage stellen, erhalten sie die gleiche Antwort.
* **Filterung statischer Informationen auf der Grundlage von Metainformationen:** Fügen Sie [Metadatentags](./tutorials/multiple-domains.md) hinzu, um zusätzliche Filteroptionen bereitzustellen, die für die Benutzer Ihrer Clientanwendung und die Informationen relevant sind. Zu den gängigen Metadateninformationen zählen [Geplauder](./how-to/chit-chat.md), Inhaltstyp oder -format, Inhaltszweck und Inhaltsaktualität. <!--TODO: Fix Link-->
* **Verwaltung einer Botkonversation, die statische Informationen beinhaltet:** Ihre Wissensdatenbank liefert eine Antwort für den Konversationstext oder Befehl eines Benutzers. Ist die Antwort Teil eines vordefinierten Konversationsablaufs (dargestellt in Ihrer Wissensdatenbank mit [Mehrfachdurchlauf-Kontext](./tutorials/guided-conversations.md)), kann der Bot diesen Ablauf problemlos bereitstellen.

## <a name="what-is-a-knowledge-base"></a>Was ist eine Wissensdatenbank?

„Fragen und Antworten“ [importiert Ihre Inhalte](./how-to/manage-knowledge-base.md) in eine Wissensdatenbank, die aus Frage-Antwort-Paaren besteht. Im Zuge des Importvorgangs werden Informationen zur Beziehung zwischen den Teilen Ihrer strukturierten und teilweise strukturierten Inhalte extrahiert, um Beziehungen zwischen den Frage-Antwort-Paaren zu implizieren. Sie können diese Frage-Antwort-Paare bearbeiten oder neue hinzufügen.

Der Inhalt des Frage-Antwort-Paars umfasst Folgendes:
* Alle alternativen Formen der Frage
* Metadatentags zum Filtern von Antwortoptionen während der Suche
* Folgeaufforderungen zum weiteren Optimieren der Suche

Nach der Veröffentlichung Ihrer Wissensdatenbank sendet eine Clientanwendung eine Benutzerfrage an Ihren Endpunkt. Der Fragen und Antworten-Dienst verarbeitet die Frage und gibt die beste Antwort zurück.

## <a name="create-a-chat-bot-programmatically"></a>Programmgesteuertes Erstellen eines Chatbots

Nach der Veröffentlichung einer Fragen und Antworten-Wissensdatenbank sendet eine Clientanwendung eine Frage an den Endpunkt Ihrer Wissensdatenbank und erhält die Ergebnisse in Form einer JSON-Antwort. Eine gängige Clientanwendung für die Beantwortung von Fragen ist ein Chatbot.

![Stellen einer Frage an einen Bot und Erhalten einer Antwort aus dem Inhalt der Wissensdatenbank](../../qnamaker/media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Schritt|Aktion|
|:--|:--|
|1|Die Clientanwendung sendet die frei formulierte _Frage_ des Benutzers „How do I programmatically update my Knowledge Base?“ (Wie kann ich meine Wissensdatenbank programmgesteuert aktualisieren?) an den Endpunkt Ihrer Wissensdatenbank.|
|2|„Fragen und Antworten“ verwendet die trainierte Wissensdatenbank, um die korrekte Antwort sowie ggf. weitere Folgeäußerungen bereitzustellen, die zur Eingrenzung der Suche nach der besten Antwort beitragen. „Fragen und Antworten“ gibt eine Antwort im JSON-Format zurück.|
|3|Die Clientanwendung entscheidet auf der Grundlage der JSON-Antwort über den weiteren Verlauf der Konversation. Mögliche Entscheidungen wären etwa, die beste Antwort anzuzeigen und weitere Optionen zu präsentieren, um die Suche nach der besten Antwort einzugrenzen. |
|||

## <a name="build-low-code-chat-bots"></a>Erstellen von Chatbots mit wenig Code

Das Language Studio-Portal stellt umfassende Funktionen für die Erstellung von Projekten/Wissensdatenbanken zur Verfügung. Sie können Dokumente in ihrer aktuellen Form in Ihre Wissensdatenbank importieren. Diese Dokumente (häufig gestellte Fragen, Handbücher, Arbeitsblätter, Webseiten oder Ähnliches) werden in Frage-Antwort-Paaren konvertiert. Jedes Paar wird nach Folgeaufforderungen analysiert und mit anderen Paaren verknüpft. Das endgültige _Markdownformat_ unterstützt eine hochwertige Darstellung mit Bildern und Links.

Veröffentlichen Sie die Wissensdatenbank nach der Bearbeitung ganz ohne Programmieraufwand für einen funktionierenden [Azure-Web-App-Bot](https://azure.microsoft.com/services/bot-service/). Testen Sie Ihren Bot im [Azure-Portal](https://portal.azure.com), oder setzen Sie die Entwicklung nach dem Herunterladen fort.

## <a name="high-quality-responses-with-layered-ranking"></a>Hochwertige Antworten mit Bewertung mit mehreren Ebenen

Das Fragen und Antworten-System verwendet einen mehrstufigen Bewertungsansatz. Die Daten werden in Azure Search gespeichert. Dies ist auch gleichzeitig die erste Bewertungsebene. Die besten Ergebnisse von Azure Search werden dann an das NLP-Neubewertungsmodell von „Fragen und Antworten“ übergeben, um die endgültigen Ergebnisse und die Zuverlässigkeitsbewertung zu generieren.

## <a name="multi-turn-conversations"></a>Konversationen mit Mehrfachdurchläufen

„Fragen und Antworten“ ermöglicht mehrteilige Äußerungen und aktives Lernen, um Sie bei der Verbesserung Ihrer grundlegenden Frage-Antwort-Paare zu unterstützen.

**Eingabeaufforderungen mit Mehrfachdurchläufen** ermöglichen die Verknüpfung von Frage-Antwort-Paaren. Diese Verknüpfung ermöglicht es der Clientanwendung, eine Top-Antwort zurückzugeben, und stellt weitere Fragen bereit, um die Suche nach einer abschließenden Antwort einzugrenzen.

Nachdem die Wissensdatenbank Benutzerfragen über den veröffentlichten Endpunkt erhalten hat, wendet „Fragen und Antworten“ **aktives Lernen** auf diese Fragen aus der Praxis an, um Änderungen an Ihrer Wissensdatenbank vorzuschlagen, die zur Verbesserung der Qualität beitragen.

## <a name="development-lifecycle"></a>Lebenszyklus der Entwicklung

„Fragen und Antworten“ bietet Erstellungs-, Trainings- und Veröffentlichungsfunktionen sowie Zusammenarbeitsberechtigungen und lässt sich somit in den gesamten Entwicklungszyklus integrieren.

> [!div class="mx-imgBorder"]
> ![Konzeptuelle Darstellung des Entwicklungszyklus](../../qnamaker/media/qnamaker-overview-learnabout/development-cycle.png)

## <a name="complete-a-quickstart"></a>Abschließen einer Schnellstartanleitung

Wir stellen Schnellstartanleitungen für die gängigsten Programmiersprachen bereit. Diese enthalten Informationen zu grundlegenden Entwurfsmustern und sind jeweils so konzipiert, dass Sie in weniger als zehn Minuten über ausführbaren Code verfügen.

* [Erste Schritte mit der Clientbibliothek für Fragen und Antworten](./quickstart/sdk.md)

## <a name="next-steps"></a>Nächste Schritte
„Fragen und Antworten“ bietet alles, was Sie zum Erstellen, Verwalten und Bereitstellen Ihrer benutzerdefinierten Wissensdatenbank benötigen.

> [!div class="nextstepaction"]
> [Überprüfen der aktuellen Änderungen](../whats-new.md)
