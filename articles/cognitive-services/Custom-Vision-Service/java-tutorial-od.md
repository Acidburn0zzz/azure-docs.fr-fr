---
title: 'Démarrage rapide : Créer un projet de détection d’objets à l’aide du kit SDK Custom Vision pour Java'
titlesuffix: Azure Cognitive Services
description: Créez un projet, ajoutez des balises, chargez des images, entraînez votre projet et détectez des objets avec le Kit de développement logiciel (SDK) Java.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 10/31/2018
ms.author: areddish
ms.openlocfilehash: a9ff0ae5488dfcda747d25724ef9917ba1a1f472
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872286"
---
# <a name="quickstart-create-an-object-detection-project-with-the-custom-vision-sdk-for-java"></a>Démarrage rapide : Créer un projet de détection d’objets à l’aide du kit SDK Custom Vision pour Java

Cet article fournit des informations et un exemple de code pour vous aider à prendre en main le Kit de développement logiciel (SDK) Custom Vision avec Java, afin de générer un modèle de détection d’objet. Une fois le projet créé, vous pouvez ajouter des régions balisées, charger des images, effectuer l’apprentissage du projet, obtenir l’URL du point de terminaison de prédiction par défaut du projet et utiliser le point de terminaison pour tester une image par programmation. Utilisez cet exemple comme modèle pour générer votre propre application Java. 

## <a name="prerequisites"></a>Prérequis

- Un IDE Java de votre choix
- [JDK 7 ou 8](https://aka.ms/azure-jdks) est installé.
- Maven est installé

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>Obtenir le Kit de développement logiciel (SDK) Custom Vision et un exemple de code
Pour écrire une application Java qui utilise Custom Vision, vous avez besoin des packages maven Custom Vision. Ils sont inclus dans l’exemple de projet que vous allez télécharger, mais vous pouvez y accéder individuellement ici.

Vous pouvez installer le SDK Vision personnalisée à partir du référentiel central Maven :
* [SDK d’entraînement](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-training)
* [SDK de prédiction](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customvision-prediction)

Clonez ou téléchargez le projet [Cognitive Services Java SDK Samples](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master) (Exemples de Kit de développement logiciel (SDK) Java Cognitive Services). Accédez au dossier **Vision/CustomVision/**.

Ce projet Java crée un projet de détection d’objet Custom Vision nommé __Sample Java OD Project__ (Exemple de projet OD Java), accessible via le [site web Custom Vision](https://customvision.ai/). Elle charge ensuite les images pour entraîner et tester un classifieur. Dans ce projet, le classifieur a pour but de déterminer si l’arbre est une __cigüe__ ou un __cerisier du Japon__.

[!INCLUDE [get-keys](includes/get-keys.md)]

Le programme est configuré de manière à stocker vos données de clé en tant que variables d’environnement. Définissez ces variables en accédant au dossier **Vision/CustomVision** dans PowerShell. Entrez ensuite les commandes :

```PowerShell
$env:AZURE_CUSTOMVISION_TRAINING_API_KEY ="<your training api key>"
$env:AZURE_CUSTOMVISION_PREDICTION_API_KEY ="<your prediction api key>"
```

## <a name="understand-the-code"></a>Comprendre le code

Chargez le projet `Vision/CustomVision` dans votre IDE Java, puis ouvrez le fichier _CustomVisionSamples.java_. Rechercher la méthode **runSample** et commentez l’appel de la méthode **ImageClassification_Sample** &mdash; cela permet d’exécuter le scénario de classification d’images, qui n’est pas abordé dans ce guide. La méthode **ObjectDetection_Sample** implémente la fonctionnalité principale de ce démarrage rapide ; accédez à sa définition et examinez le code. 

### <a name="create-a-new-custom-vision-service-project"></a>Créer un projet Custom Vision

Accédez au bloc de code qui crée un client d’entraînement et un projet de détection d’objet. Le projet créé apparaît sur le [site web Custom Vision](https://customvision.ai/) sur lequel vous êtes allé plus tôt. 

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=181-206)]

### <a name="add-tags-to-your-project"></a>Ajouter des mots clés à votre projet

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=208-218)]

### <a name="upload-and-tag-images"></a>Charger et étiqueter des images

Lorsque vous appliquez des balises à des images dans des projets de détection d’objet, vous devez préciser la région de chaque objet balisé avec des coordonnées normalisées. Accédez à la définition de la carte `regionMap`. Ce code associe chaque exemple d’image à sa région balisée.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=130-179)]

Ensuite, passez au bloc de code qui ajoute les images au projet. Les images sont lues à partir du dossier **src/main/ressources** du projet et sont chargées dans le service avec leurs balises et coordonnées de la région appropriées.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=220-231)]

L’extrait de code précédent utilise deux fonctions d’assistance qui récupèrent les images comme flux de ressources et les charge dans le service.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=277-314)]

### <a name="train-the-project"></a>Entraîner le projet

Ce code crée la première itération du projet et la marque comme l’itération par défaut. L’itération par défaut correspond à la version du modèle qui répondra aux requêtes de prédiction. Vous devez la mettre à jour à chaque fois que vous réentraînez le modèle.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=233-242)]

### <a name="use-the-prediction-endpoint"></a>Utiliser le point de terminaison de prédiction

Le point de terminaison de prédiction, représenté par l’objet `predictor` ici, est la référence que vous pouvez utiliser pour soumettre une image au modèle actuel et obtenir une prédiction de classification. Dans cet exemple, `predictor` est défini ailleurs à l’aide de la variable d’environnement de la clé de prédiction.

[!code-java[](~/cognitive-services-java-sdk-samples/Vision/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?range=244-270)]

## <a name="run-the-application"></a>Exécution de l'application

Pour compiler et exécuter la solution à l’aide de maven, exécutez la commande suivante dans le répertoire du projet dans PowerShell :

```PowerShell
mvn compile exec:java
```

Affichez la sortie de la console pour la journalisation et les résultats de prédiction. Vous pouvez alors vérifier que l’image test est balisée de façon appropriée et que la région de détection est correcte.

[!INCLUDE [clean-od-project](includes/clean-od-project.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous savez maintenant comment effectuer la détection d’objet dans le code. Cet exemple exécute une itération d’entraînement unique, mais vous aurez souvent besoin d’entraîner et de tester votre modèle à plusieurs reprises pour plus de précision. Le guide suivant traite de la classification d’images, mais ses principes sont identiques à la détection d’objet.

> [!div class="nextstepaction"]
> [Tester et réentraîner un modèle](test-your-model.md)
