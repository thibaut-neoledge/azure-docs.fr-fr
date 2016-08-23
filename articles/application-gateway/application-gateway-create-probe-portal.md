<properties
   pageTitle="Créer une sonde personnalisée pour la plateforme d’application à l’aide du portail | Microsoft Azure"
   description="Découvrez comment créer une sonde personnalisée pour Application Gateway à l’aide du portail"
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
   ms.date="08/09/2016"
   ms.author="gwallace" />

# Créer une sonde personnalisée pour Application Gateway à l’aide du portail

> [AZURE.SELECTOR]
- [Portail Azure](application-gateway-create-probe-portal.md)
- [Commandes PowerShell pour Azure Resource Manager](application-gateway-create-probe-ps.md)
- [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

<BR>

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## Scénario

Le scénario suivant passe par la création d’une sonde d’intégrité personnalisée sur une passerelle d’application existante. Le scénario suppose que vous avez déjà suivi la procédure de [Création d’une passerelle Application Gateway](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Créer la sonde

Les sondes sont configurées via un processus en deux étapes sur le portail. La première étape consiste à créer la sonde, puis à l’ajouter aux paramètres http principaux de la passerelle d’application.

### Étape 1 :

Accédez au http://portal.azure.com et sélectionnez une passerelle d’application existante.

![Vue d’ensemble d’Application Gateway][1]

### Étape 2 :

Cliquez sur **Sondes** et cliquez sur le bouton **Ajouter** pour ajouter une nouvelle sonde.

![Ajouter un panneau Sonde contenant toutes les informations][2]

### Étape 3

Remplissez les informations requises pour la sonde et lorsque vous avez terminé, cliquez sur **OK**.

- **Nom** - nom convivial de la sonde accessible via le portail.
- **Hôte** - nom d’hôte utilisé pour la sonde.
- **Chemin d’accès** - reste de l’URL complète de la sonde personnalisée.
- **Intervalle (secondes)** - fréquence d’exécution de la sonde pour vérifier l’intégrité.
- **Délai d’expiration (secondes)** - intervalle de temps précédant l’expiration de la sonde.
- **Seuil de défaillance sur le plan de l’intégrité** - nombre d’échecs nécessaires pour marquer l’instance comme défaillante.

> [AZURE.IMPORTANT] le nom d’hôte n’est pas le nom du serveur. Il s’agit du nom de l’hôte virtuel en cours d’exécution sur le serveur d’application. La sonde est envoyée à http://(host name):(port from httpsetting)/urlPath

![paramètres de configuration de sonde][3]

## Ajouter une sonde à la passerelle

Maintenant que la sonde a été créée, il est temps de l’ajouter à la passerelle. Les paramètres de la sonde sont définis sur les paramètres http principaux de la passerelle d’application.

### Étape 1 :

Cliquez sur les **paramètres HTTP** de la passerelle d’application, puis cliquez sur les paramètres http principaux actuels dans la fenêtre pour afficher le panneau de configuration.

![fenêtre de paramètres https][4]

### Étape 2 :

Dans le panneau Paramètres **appGatewayBackEndHttp**, cliquez sur **Utiliser la sonde personnalisée** et choisissez la sonde créée à la section [Créer la sonde](#createprobe). Lorsque vous avez terminé, cliquez sur **OK** pour appliquer les paramètres.

![panneau de paramètres appgatewaybackend][5]

La sonde par défaut vérifie l’accès par défaut à l’application web. Maintenant qu’une sonde personnalisée a été créée, la passerelle d’application utilise le chemin d’accès personnalisé défini pour surveiller l’intégrité pour le serveur principal sélectionné. Selon les critères définis, la passerelle d’application vérifie le fichier spécifié dans la sonde. Si l’appel à host:Port/path ne renvoie pas de réponse d’état Http 200 OK, le serveur est mis hors service après avoir atteint le seuil de fonctionnement anormal. La détection continue sur l’instance défaillante pour déterminer quand elle sera rétablie. Une fois l’instance ajoutée au pool de serveurs intègre, le trafic recommence à circuler vers elle et la détection de l’instance continue comme d’habitude en fonction des intervalles définis par l’utilisateur.


## Étapes suivantes

Pour découvrir comment configurer le déchargement SSL avec la passerelle Azure Application Gateway, consultez [Configuration du déchargement SSL](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png

<!---HONumber=AcomDC_0810_2016-->