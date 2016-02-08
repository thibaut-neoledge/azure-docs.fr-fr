<properties 
   pageTitle="Configuration d’une passerelle Application Gateway pour l’utilisation de sondes personnalisées à l’aide d’Azure Resource Manager | Microsoft Azure"
   description="Cette page fournit des instructions pour la configuration de sondes personnalisées sur Application Gateway à l’aide d’Azure Resource Manager"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>

# Surveillance de l’intégrité et sondes personnalisées 


Azure Application Gateway analyse l’intégrité de toutes les ressources de son pool principal et supprime automatiquement du pool les ressources considérées comme défectueuses.

Vous pouvez configurer la surveillance d’intégrité à l’aide de deux types de sondes : la sonde d’intégrité par défaut ou une sonde personnalisée.

## Sonde d’intégrité par défaut

Une passerelle d’application configure automatiquement une sonde d’intégrité par défaut lorsque vous ne définissez pas de configuration de sonde personnalisée. La surveillance suppose d’envoyer une requête HTTP aux adresses IP configurées pour le pool principal et pour le port configuré dans le paramètre HTTP principal de la passerelle d’application.

Par exemple : vous configurez votre passerelle d’application pour utiliser des serveurs principaux A, B et C afin de recevoir le trafic réseau HTTP sur le port 80. Les contrôles de défaillance par défaut testent les trois serveurs toutes les 30 secondes pour obtenir une réponse HTTP correcte. Le [code d’état](https://msdn.microsoft.com/library/aa287675.aspx) d’une réponse HTTP correcte est compris entre 200 et 399.

Si l’analyse de la sonde par défaut échoue pour le serveur A, la passerelle d’application le retire de son pool principal et le trafic réseau cesse de transiter par ce serveur. La sonde par défaut continue de contrôler le serveur A toutes les 30 secondes. Lorsque le serveur A répond correctement à la requête de la sonde d’intégrité, il réintègre le pool principal à l’état sain, et le trafic reprend dans le serveur.

La sonde par défaut utilise uniquement les adresses IP pour vérifier l’état. Si vous souhaitez tester la connectivité sur une URL pour le contrôle d’intégrité, vous devez utiliser une sonde personnalisée.


## Sonde personnalisée 

Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de la surveillance de l’état. En utilisant des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL et le chemin à tester et le nombre de réponses en échec autorisé avant que l’instance de pool principal soit marquée comme étant défectueuse.


Paramètres de la sonde personnalisée :

- **Intervalle d’analyse** : configure les intervalles d’analyse de sonde.
- **Délai d’expiration de l’analyse** : définit le délai d’expiration d’un contrôle de requête HTTP.
- **Seuil de défaillance sur le plan de l’intégrité** : spécifie le nombre d’échecs nécessaires pour marquer l’instance comme défaillante.  
- **Nom d’hôte et chemin d’accès** : si votre site web ou votre batterie de serveurs web ne renvoie pas de réponse HTTP pour l’adresse IP, vous devez configurer un nom d’hôte de sonde et un chemin d’accès pour une réponse HTTP intègre et valide. Par exemple : votre site web http://contoso.com/ ne génère pas de réponse HTTP valide. Vous devez configurer un nom d’hôte et un chemin d’accès pour renvoyer une réponse HTTP intègre et valide afin de confirmer l’intégrité de l’instance de serveur web. Dans ce cas, la sonde personnalisée peut être configurée pour « http://contoso.com/path/custompath.htm » afin que les analyses renvoient une réponse HTTP réussie. 



>[AZURE.NOTE] Les sondes personnalisées ne peuvent être configurées qu’à l’aide de PowerShell

<!---HONumber=AcomDC_0128_2016-->