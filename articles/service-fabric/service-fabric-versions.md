---
title: Découvrir les versions de cluster d’Azure Service Fabric | Microsoft Docs
description: Versions de cluster Azure Service Fabric prises en charge
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/24/2018
ms.author: aljo
ms.openlocfilehash: de5522e68d1329ce2b80a4d3c7045d38c13169e5
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57191645"
---
# <a name="supported-service-fabric-versions"></a>Versions de Service Fabric prises en charge

Veillez à ce que votre cluster exécute toujours une version prise en charge de Service Fabric. Lorsque nous annonçons le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de lancement. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).

Consultez les documents suivants pour savoir plus en détails comment faire en sorte que votre cluster exécute toujours une version prise en charge de Service Fabric.

- [Version mise à niveau de Service Fabric sur un cluster Azure](service-fabric-cluster-upgrade.md)
- [Mettre à niveau la version de Service Fabric sur un cluster autonome windows server](service-fabric-cluster-upgrade-windows-server.md)

Voici la liste des versions de Service Fabric prises en charge et la date associée de fin de prise en charge.

| **Runtime Service Fabric dans le cluster** | **Version de cluster à partir de laquelle une mise à niveau directe est possible** |**Kit de développement logiciel compatible / Versions de package NuGet** | **Date de fin de prise en charge** |
| --- | --- |--- | --- |
| Toutes les versions de clusters antérieures à 5.3.121 | 5.1.158* |Inférieure ou égale à la version 2.3 |20 janvier 2017 |
| 5.3.* | 5.1.158.* |Inférieure ou égale à la version 2.3 |24 février 2017 |
| 5.4.* | 5.1.158.* |Inférieure ou égale à la version 2.4 |10 mai 2017       |
| 5.5.* | 5.4.164.* |Inférieure ou égale à la version 2.5 |10 août 2017    |
| 5.6.* | 5.4.164.* |Inférieure ou égale à la version 2.6 |13 octobre 2017   |
| 5.7.* | 5.4.164.* |Inférieure ou égale à la version 2.7 |15 décembre 2017  |
| 6.0.* | 5.6.205.* |Inférieure ou égale à la version 2.8 |30 mars 2018     |
| 6.1.* | 5.7.221.* |Inférieure ou égale à la version 3.0 |15 juillet 2018      |
| 6.2.* | 6.0.232.* |Inférieure ou égale à la version 3.1 |26 octobre 2018   |
| 6.3.* | 6.1.480.* |Inférieure ou égale à la version 3.2 |Mars 31,2019  |
| 6.4.* | 6.2.301.* |Inférieure ou égale à la version 3.3 |Version actuelle ; par conséquent, pas de date de fin |