---
title: Codes de réponse HTTP d’API
titleSuffix: Azure
description: Comprendre les codes de réponse HTTP retournés par les API de point de terminaison et de création LUIS
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/04/2019
ms.author: diberry
ms.openlocfilehash: 18ec59200d3cf820794ac353e38106ad26aca697
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337242"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Codes de réponse d’API courants et leur signification

Les API de [création](https://aka.ms/luis-authoring-apis) et de [point de terminaison](https://aka.ms/luis-endpoint-apis) retournent des codes de réponse HTTP. Bien que les messages de réponse contiennent des informations propres à une requête, le code d’état de réponse HTTP est général. 

## <a name="common-status-codes"></a>Codes d’état courants
Le tableau suivant liste certains des codes d’état de réponse HTTP les plus courants pour les API de [création](https://aka.ms/luis-authoring-apis) et de [point de terminaison](https://aka.ms/luis-endpoint-apis) :

|Code|API|Explication|
|:--|--|--|
|400|Création, Point de terminaison|Les paramètres de la requête sont incorrects, ce qui signifie que les paramètres obligatoires sont manquants, incorrects ou trop grands|
|400|Création, Point de terminaison|Le corps de la requête est incorrect, ce qui signifie que l’objet JSON est manquant, mal formé ou trop grand|
|401|Création|Vous avez utilisé une clé d’abonnement de point de terminaison au lieu d’une clé de création|
|401|Création, Point de terminaison|La clé est vide, mal formée ou non valide|
|401|Création, Point de terminaison| La clé ne correspond pas à la région|
|401|Création|Vous n’êtes pas propriétaire ou collaborateur|
|401|Création|L’ordre des appels d’API n’est pas valide|
|403|Création, Point de terminaison|La limite de quota mensuel de clés est dépassée|
|409|Point de terminaison|L’application est encore en cours de chargement|
|410|Point de terminaison|L’application doit être ré-entraînée et republiée|
|414|Point de terminaison|La requête dépasse la limite maximale du nombre de caractères|
|429|Création, Point de terminaison|Le débit maximal est dépassé (requêtes/seconde)|

## <a name="next-steps"></a>Étapes suivantes

* Documentation sur la [création](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) et le [point de terminaison](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) d’API REST
