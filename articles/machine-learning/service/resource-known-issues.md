---
title: Problèmes connus et résolutions
titleSuffix: Azure Machine Learning service
description: Liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Machine Learning service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: fdc718429991a7bba30739d09882c51f8baf5dc9
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336267"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problèmes connus et dépannage du service Azure Machine Learning

Cet article vous permet de rechercher et de corriger les erreurs ou les défaillances rencontrées lors de l’utilisation du service Azure Machine Learning.

## <a name="sdk-installation-issues"></a>Problèmes d’installation de Kit de développement logiciel (SDK)

**Message d’erreur : Impossible de désinstaller « PyYAML »**

Kit SDK Azure Machine Learning pour Python : PyYAML est un projet installé distutils. Par conséquent, nous ne pouvons pas déterminer avec précision les fichiers qui lui appartiennent en cas de désinstallation partielle. Pour poursuivre l’installation du kit de développement logiciel (SDK) en ignorant cette erreur, utilisez :

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problèmes à la création de la Capacité de calcul Azure Machine Learning

Il peut arriver, dans de rares cas, que des utilisateurs ayant créé leur espace de travail Azure Machine Learning sur le Portail Azure avant la disponibilité générale ne puissent pas créer de Capacité de calcul Azure Machine Learning dans cet espace de travail. Vous pouvez soulever une demande de support auprès du service ou créer un espace de travail sur le Portail ou avec le kit SDK pour vous débloquer sans délai.

## <a name="image-building-failure"></a>Échec de génération d’image

Échec de génération d’image lors du déploiement de service web. La solution de contournement consiste à ajouter « pynacl==1.2.1 » en tant que dépendance pip au fichier Conda pour la configuration d’image.

## <a name="deployment-failure"></a>Échec du déploiement

Si vous observez `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, remplacez la référence SKU des machines virtuelles utilisées dans votre déploiement par une référence SKU disposant de plus de mémoire.

## <a name="fpgas"></a>FPGA

Vous ne serez pas en mesure de déployer des modèles sur des FPGA tant que vous n’aurez pas demandé un quota FPGA et qu’il n’aura pas été approuvé. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai

## <a name="databricks"></a>Databricks

Problèmes Databricks et Azure Machine Learning

### <a name="failure-when-installing-packages"></a>Échec lors de l’installation des packages

Installation d’Azure Machine Learning SDK échoue sur Azure Databricks lorsque plusieurs packages sont installés. Certains packages, comme `psutil`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en gelant la version de la bibliothèque. Ce problème est lié à Databricks et pas pour le SDK du service Azure Machine Learning. Vous pouvez rencontrer ce problème avec d’autres bibliothèques, trop. Exemple :

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Vous pouvez également utiliser des scripts init si vous conservez face à des problèmes d’installation avec les bibliothèques Python. Cette approche n’est pas officiellement pris en charge. Pour plus d’informations, consultez [scripts init à portée d’un Cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Annuler une série d’apprentissage automatique

Lorsque vous utilisez automatisée des fonctionnalités machine learning sur Azure Databricks, pour annuler une exécution et démarrez une nouvelle expérience à exécuter, redémarrez votre cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 itérations pour l’apprentissage automatique

Dans automatisé machine learning de paramètres, si vous avez plus de 10 itérations, définissez `show_output` à `False` lorsque vous envoyez l’exécution.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget pour l’apprentissage automatique/des SDK Azure Machine Learning

Le widget de kit de développement logiciel Azure Machine Learning n’est pas pris en charge dans une instance Databricks notebook, car les ordinateurs portables ne peut pas analyser les widgets HTML. Vous pouvez afficher le widget dans le portail à l’aide de ce code Python dans la cellule du bloc-notes Azure Databricks :

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erreur d’importation : Aucun module nommé « pandas.core.indexes »

Si vous voyez cette erreur lorsque vous utilisez automatisée apprentissage :

1. Exécutez cette commande pour installer deux packages dans votre cluster Azure Databricks : 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Déconnectez, puis reconnectez le cluster à votre ordinateur portable. 

Si cela ne résout pas le problème, essayez de redémarrer le cluster.

## <a name="azure-portal"></a>Portail Azure

Si vous accédez directement à votre espace de travail à partir d’un lien de partage provenant du kit SDK ou du portail, vous ne pourrez pas afficher la page Vue d’ensemble normale comportant des informations sur l’abonnement dans l’extension. Vous ne pourrez pas non plus basculer sur un autre espace de travail. Si vous souhaitez afficher un autre espace de travail, la solution de contournement consiste à accéder directement au [portail Azure](https://portal.azure.com) et à rechercher le nom de l’espace de travail.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile.
Les fichiers journaux se trouvent aux emplacements suivants :

## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="authentication-errors"></a>Erreurs d’authentification

Si vous effectuez une opération de gestion sur une cible de calcul à partir d'un travail distant, vous recevrez l'une des erreurs suivantes :

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Par exemple, vous recevrez une erreur si vous essayez de créer ou de joindre une cible de calcul à partir d'un pipeline Machine Learning soumis en vue d'une exécution à distance.

## <a name="get-more-support"></a>Obtenir plus de support

Vous pouvez envoyez des demandes de support et obtenir de l’aide auprès du support technique, des forums, etc. [En savoir plus…](support-for-aml-services.md)
