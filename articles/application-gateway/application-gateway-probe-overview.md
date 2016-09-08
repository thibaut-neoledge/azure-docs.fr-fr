

<properties
   pageTitle="Présentation de la surveillance de défaillance pour Azure Application Gateway | Microsoft Azure"
   description="En savoir plus sur les capacités d’analyse dans Azure Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
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
   ms.date="08/19/2016"
   ms.author="gwallace" />

# Vue d’ensemble de l’analyse d’intégrité Application Gateway


Azure Application Gateway analyse par défaut l’intégrité de toutes les ressources de son pool principal et supprime automatiquement du pool les ressources considérées comme défectueuses. Application Gateway continue de surveiller les instances défaillantes et les réintroduit dans le pool principal intègre une fois qu’elles redeviennent disponibles et répondent aux sondes d’intégrité.

En plus d’utiliser la surveillance par sonde d’intégrité par défaut, vous pouvez aussi personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Dans cet article, nous allons nous intéresser aux sondes d’intégrité par défaut et personnalisées.

## Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez pas de configuration de sonde personnalisée. Le comportement d’analyse par défaut consiste à lancer une requête HTTP aux adresses IP configurées pour le pool principal.

Par exemple : vous configurez votre passerelle d’application pour utiliser les serveurs principaux A, B et C, qui recevront le trafic réseau HTTP sur le port 80. Les contrôles de défaillance par défaut testent les trois serveurs toutes les 30 secondes pour obtenir une réponse HTTP correcte. Le [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) d’une réponse HTTP correcte est compris entre 200 et 399.

Si l’analyse de la sonde par défaut échoue pour le serveur A, la passerelle d’application le retire de son pool principal et le trafic réseau cesse de passer par ce serveur. La sonde par défaut continue de contrôler le serveur A toutes les 30 secondes. Dès que le serveur A répond avec succès à une requête de la sonde d’intégrité par défaut, il est réintroduit dans le pool principal en tant que serveur intègre et le trafic vers celui-ci reprend.

La sonde par défaut examine uniquement http://127.0.0.1:\<port> pour déterminer l’état d’intégrité. Si vous devez configurer la sonde d’intégrité de sorte qu’elle accède à une URL personnalisée ou modifier d’autres paramètres, vous devez utiliser des sondes personnalisées comme décrit ci-dessous.

### Paramètres de sonde d’intégrité par défaut

|Propriétés de la sonde | Valeur | Description|
|---|---|---|
| URL de sonde| http://127.0.0.1/ | Chemin d'accès de l'URL |
| Intervalle | 30 | Intervalle d’analyse en secondes |
| Délai d’attente | 30 | Délai d’expiration de l’analyse en secondes |
| Seuil de défaillance sur le plan de l’intégrité | 3 | Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |


## Sonde d’intégrité personnalisée

Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de l’analyse de l’intégrité. En utilisant des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL et le chemin à tester et le nombre de réponses en échec autorisé avant que l’instance de pool principal soit marquée comme étant défectueuse.


### Paramètres de sonde d’intégrité personnalisée

|Propriétés de la sonde| Description|
|---|---|
| Name | Nom de la sonde. Ce nom est utilisé pour désigner la sonde dans les paramètres HTTP du serveur principal. |
| Protocole | Protocole utilisé pour envoyer la sonde. Le protocole HTTP est le seul protocole valide. |
| Host | Nom d’hôte pour l’envoi de la sonde. |
| Chemin | Chemin relatif de la sonde. Le chemin valide commence par « / ». La sonde est envoyée à <protocole>://<hôte>:<port><chemin d’accès> |
| Intervalle | Intervalle d’analyse en secondes. Il s’agit de l’intervalle de temps qui s’écoule entre deux analyses consécutives.|
| Délai d’attente | Délai d’expiration de l’analyse en secondes. La sonde est marquée comme étant en échec si aucune réponse valide n’est reçue dans le délai imparti. |
| Seuil de défaillance sur le plan de l’intégrité | Nombre de tentatives d’analyse Le serveur principal est marqué comme étant défectueux après que le nombre d’échecs consécutifs a atteint le seuil de défaillance. |

## Étapes suivantes

Après vous être familiarisé avec l’analyse d’intégrité Application Gateway, vous pouvez configurer une [sonde d’intégrité personnalisée](application-gateway-create-probe-ps.md) pour Azure Resource Manager ou une [sonde d’intégrité personnalisée](application-gateway-create-probe-classic-ps.md) pour le modèle de déploiement Azure classique.

<!---HONumber=AcomDC_0824_2016-->