<properties
   pageTitle="Réinitialiser une passerelle VPN Azure | Microsoft Azure"
   description="Cet article vous guide dans le processus de réinitialisation de la passerelle VPN d’Azure. Notez que cet article s’applique aux machines virtuelles créées à l’aide du modèle de déploiement classique."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/16/2015"
   ms.author="cherylmc"/>

# Réinitialiser une passerelle VPN Azure à l’aide de PowerShell


Cet article vous guidera dans la réinitialisation de votre passerelle VPN Azure à l’aide des applets de commande PowerShell. Ces instructions s’appliquent au modèle de déploiement classique. Nous n’avons pas encore d’applet de commande ou d’API REST pour réinitialiser une passerelle VPN pour les réseaux virtuels créés à l’aide du modèle de déploiement de Resource Manager. Ils sont en cours d’élaboration. Vous pouvez savoir si votre passerelle VPN a été créée à l’aide du modèle de déploiement classique en consultant votre réseau virtuel dans le portail Azure. Les réseaux virtuels créés à l’aide du modèle de déploiement classique sont affichés dans la partie Réseau virtuel (classique) du portail Azure.

La réinitialisation de la passerelle VPN Azure est utile lorsque vous perdez une connexion VPN entre locaux sur un ou plusieurs tunnels VPN S2S. Dans ce cas, vos périphériques VPN sur site fonctionnent tous correctement, mais ils ne sont pas en mesure d’établir des tunnels IPsec avec les passerelles VPN Azure. Lorsque vous utilisez l’applet de commande *Reset-AzureVNetGateway*, elle redémarre la passerelle et lui réapplique les configurations entre locaux. La passerelle conserve l’adresse IP publique qu’elle a déjà. Cela signifie que vous n’avez pas à mettre à jour la configuration du routeur VPN avec une nouvelle adresse IP publique pour la passerelle VPN Azure.


Avant de réinitialiser votre passerelle, vérifiez les éléments clés répertoriés ci-dessous pour chaque tunnel VPN IPsec site à site (S2S). Toute incohérence dans les éléments entraîne la déconnexion des tunnels VPN S2S. Une vérification et une correction des configurations pour vos passerelles locales et vos passerelles VPN Azure vous évitent des redémarrages et des perturbations sur les autres connexions en cours sur les passerelles.

Vérifiez les éléments suivants avant de réinitialiser votre passerelle.

- Les adresses IP Internet (VIP) de la passerelle VPN Azure et de la passerelle VPN locale sont correctement configurées dans Azure et dans les stratégies VPN sur site.
- La clé prépartagée doit être la même sur la passerelle VPN Azure et la passerelle VPN locale.
- Si vous appliquez une configuration IPsec/IKE spécifique, telle que le chiffrement, des algorithmes de hachage et PFS (Perfect Forward Secrecy), vérifiez que les passerelles VPN Azure et locale ont les mêmes configurations.


## Réinitialiser une passerelle VPN à l’aide de PowerShell

L’applet de commande PowerShell servant à réinitialiser la passerelle VPN Azure est *Reset-AzureVNetGateway*. Chaque passerelle VPN Azure se compose de deux instances de machines virtuelles en cours d’exécution dans une configuration de serveur de secours actif. Une fois la commande émise, l’instance de la passerelle VPN Azure active actuelle est immédiatement redémarrée. Un bref laps de temps s’écoule pendant le basculement de l’instance active (en cours de redémarrage) vers l’instance de serveur de secours. Cet intervalle doit être inférieur à une minute.

L’exemple qui suit permet de réinitialiser la passerelle VPN Azure du réseau virtuel appelé « ContosoVNet ».
 
			D:\PS> Reset-AzureVNetGateway –VnetName “ContosoVNet” 

	 		Error          :
	 		HttpStatusCode : OK
	 		Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
	 		Status         : Successful
			RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
			StatusCode     : OK


Si la connexion n’est pas restaurée après le premier redémarrage, exécutez de nouveau la commande pour redémarrer la deuxième instance de machine virtuelle (la nouvelle passerelle active). Si les deux redémarrages sont demandés à la suite, il s’écoulera un délai un peu plus long pendant lequel les deux instances de machine virtuelle machine virtuelles (active/veille) sont en cours de redémarrage. Dans ce cas, l’intervalle de connectivité VPN permettant aux machines virtuelles de terminer les redémarrages sera un peu plus long, jusqu’à 2 à 4 minutes.

Après deux redémarrages, si vous continuez de rencontrer des problèmes de connectivité entre locaux, ouvrez un ticket de support à partir du Portail Azure Classic pour contacter le support technique de Microsoft Azure.


## Étapes suivantes
	
Consultez la [référence PowerShell](https://msdn.microsoft.com/library/azure/mt270366.aspx) pour plus d’informations sur cette applet de commande.

<!---HONumber=Nov15_HO4-->