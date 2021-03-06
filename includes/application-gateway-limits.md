---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 5ad1339c04444bcb4cc550be26e239e65227d2ce
ms.sourcegitcommit: fbfe56f6069cba027b749076926317b254df65e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58494791"
---
| Ressource | Limite par défaut | Remarque |
| --- | --- | --- |
| Azure Application Gateway |1 000 par abonnement | |
| Configurations IP frontales |2 |1 publique et 1 privée |
| Ports frontaux |100<sup>1</sup> | |
| Pool d'adresses principales |100<sup>1</sup> | |
| Serveurs principaux par pool |1,200 | |
| Écouteurs HTTP |100<sup>1</sup> | |
| Règles d’équilibrage de charge HTTP |100<sup>1</sup> | |
| Paramètres HTTP du serveur principal |100<sup>1</sup> | |
| Instances par passerelle |32 | |
| Certificats SSL |100<sup>1</sup> |1 par écouteur HTTP |
| Certificats d’authentification |100 | |
| Certificats racines approuvés |100 | |
| Délai d’expiration de requête minimale |1 seconde | |
| Délai d’expiration de la demande maximale |24 heures | |
| Nombre de sites |100<sup>1</sup> |1 par écouteur HTTP |
| Mappages d’URL par écouteur |1 | |
| Nombre maximal de règles basées sur le chemin par mappage d’URL|100||
| Configurations de redirection |100<sup>1</sup>| |
| Connexions WebSocket simultanées |Passerelles moyenne 20 Ko<br> Passerelles volumineux 50k| |
| Longueur maximale d’URL|8 000||
| Taille de téléchargement de fichier maximale, Standard |2 Go | |
| Taille de téléchargement maximale WAF |Passerelles WAF moyenne, 100 Mo<br>Passerelles WAF volumineux, 500 Mo| |
| Limite de taille du corps de WAF, sans les fichiers|128 Ko||

<sup>1</sup> en cas de références avec WAF activé, nous vous recommandons de limiter le nombre de ressources à 40 pour des performances optimales.
