

<properties 
   pageTitle="Présentation de la surveillance de défaillance pour Azure Application Gateway | Microsoft Azure"
   description="En savoir plus sur les capacités d’analyse dans Azure Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Présentation de l’analyse d’intégrité de la passerelle d’application 


Azure Application Gateway analyse par défaut l’état de l’ensemble des ressources de son pool principal et supprime automatiquement toutes les ressources considérées comme défectueuses du pool. Application Gateway continue de surveiller les instances défaillantes et les renvoie dans le pool principal sain une fois qu’elles redeviennent disponibles et répondent aux sondes d’intégrité.

Outre la surveillance par sonde d’intégrité par défaut, vous avez la possibilité de personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Dans cet article, nous allons étudier les sondes d’intégrité par défaut et personnalisées.

## Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez pas de configuration de sonde personnalisée. Le comportement par défaut fonctionne en lançant une requête HTTP aux adresses IP configurées pour le pool principal.

Par exemple : vous configurez votre passerelle d’application pour utiliser des serveurs principaux A, B et C afin de recevoir le trafic réseau HTTP sur le port 80. Les contrôles de défaillance par défaut testent les trois serveurs toutes les 30 secondes pour obtenir une réponse HTTP correcte. Le [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) d’une réponse HTTP correcte est compris entre 200 et 399.

Si l’analyse de défaillance du serveur A échoue, la passerelle d’application la retire de son pool principal et le trafic réseau cesse de passer par ce serveur. La sonde par défaut continue de contrôler le serveur A toutes les 30 secondes. Lorsque le serveur A répond correctement à la requête de la sonde d’intégrité, il réintègre le pool principal à l’état sain, et le trafic reprend dans le serveur.

La sonde par défaut examine uniquement http://127.0.0.1:<port> pour déterminer l’état d’intégrité. Si vous devez configurer la sonde d’intégrité pour accéder à une URL personnalisée ou modifier d’autres paramètres, vous devez utiliser les sondes personnalisées comme décrit ci-dessous.

### Paramètres de sonde d’intégrité par défaut

|Propriétés de la sonde | Valeur | Description|
|---|---|---|
| URL de sonde| http://127.0.0.1/ | Chemin d'accès de l'URL |
| Intervalle | 30 | intervalle d’analyse en secondes |
| Délai d'expiration | 30 | délai d’expiration de sonde en secondes |
| Seuil de défaillance | 3 | Nombre de tentatives d’analyse Le serveur principal est marqué lorsque le nombre d’échecs consécutifs a atteint le seuil de défaillance |


## sonde d’intégrité personnalisée 

Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de la surveillance de l’état. En cas d’utilisation de sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL et le chemin d’accès à tester et le nombre de réponses d’échec à accepter avant de marquer l’instance de pool principal comme défaillante.


### Paramètres de sonde d’intégrité personnalisée

|Propriétés de la sonde| Description|
|---|---|
| Nom | Nom de la sonde Ce nom est utilisé pour désigner la sonde dans les paramètres principaux Http |
| Protocole | Protocole utilisé pour envoyer la sonde. HTTP est le seul protocole valide |
| Hôte | Nom d’hôte pour envoyer la sonde |
| Path | Chemin d’accès relatif à la sonde. Le chemin d’accès valide commence par « / ». La sonde est envoyée à <protocol>://<host>:<port><path> |
| Intervalle | Intervalle d’analyse en secondes. Il s’agit de l’intervalle de temps qui s’écoule entre deux analyses consécutives| 
| Délai d'expiration | Expiration d’analyse en secondes Sonde marquée comme ayant échoué si aucune réponse valide n’est reçue dans le délai imparti |
| Seuil de défaillance | Nombre de tentatives d’analyse Le serveur principal est marqué lorsque le nombre d’échecs consécutifs a atteint le seuil de défaillance |

## Étapes suivantes

Après avoir étudié l’analyse d’intégrité de la passerelle d’application, vous pouvez configurer une [sonde d’intégrité personnalisée](application-gateway-create-probe-ps.md) pour Azure Resource Manager ou une [sonde d’intégrité personnalisée](application-gateway-create-probe-classic-ps.md) pour le modèle de déploiement Azure classique.

<!---HONumber=AcomDC_0107_2016-->