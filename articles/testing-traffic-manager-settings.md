<properties
   pageTitle="Test de vos paramètres Traffic Manager"
   description="Cet article est destiné à vous aider à tester vos paramètres Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Test des paramètres Traffic Manager

La meilleure façon de tester vos paramètres Traffic Manager consiste à configurer plusieurs clients, puis à arrêter simultanément les points de terminaison de votre profil, qui se composent de services cloud et de sites Web. Voici quelques conseils qui vous aideront à tester votre profil Traffic Manager.

## Procédure de test de base

-**Définissez une durée de vie DNS très courte** de sorte que les modifications se répercutent rapidement, en 30 secondes, par exemple.

-**Veillez à connaître l'adresse IP de tous vos services cloud et sites Web Azure** contenus dans le profil de que vous testez.

-**Utilisez des outils permettant de résoudre un nom DNS en adresse IP** et affichez cette adresse. Vous devez vérifier que le nom de domaine de votre entreprise est résolu en adresses IP des points de terminaison contenus dans votre profil. Ils doivent être résolus de manière cohérente avec la méthode d'équilibrage de charge de votre profil Traffic Manager. Si l'ordinateur que vous utilisez exécute Windows, vous pouvez utiliser l'outil Nslookup.exe à partir d'une commande ou d'une invite de commandes Windows PowerShell. D'autres outils publics permettant d'obtenir les adresses IP sont également disponibles sur Internet.

### Pour vérifier un profil Traffic Manager à l'aide de nslookup

1-Ouvrez une commande ou une invite de commandes Windows PowerShell en tant qu'administrateur.

2-Tapez `ipconfig /flushdns` pour vider le cache de résolution DNS.

3-Tapez `nslookup <your Traffic Manager domain name>`. Par exemple, la commande suivante permet de vérifier le nom de domaine doté du préfixe *myapp.contoso*
    nslookup myapp.contoso.trafficmanager.net
On obtient alors le résultat suivant :
- Le nom DNS et l'adresse IP du serveur DNS utilisé pour résoudre ce nom de domaine Traffic Manager.
- Le nom de domaine Traffic Manager que vous avez tapé dans la ligne de commande après " nslookup " et l'adresse IP à laquelle correspond le domaine Traffic Manager. La deuxième adresse IP est celle à vérifier. Elle doit correspondre à une adresse IP virtuelle (VIP) publique pour un des services cloud ou sites Web dans le profil Traffic Manager que vous testez.

## Test des méthodes d'équilibrage de charge


### Pour tester une méthode d'équilibrage de charge de basculement

1-Maintenez tous les points de terminaison à l'état actif.
2-Utilisez un seul client.
3-Demandez la résolution DNS du nom de domaine de votre entreprise à l'aide de l'outil Nslookup.exe ou d'un utilitaire similaire.
4-Assurez-vous que l'adresse IP résolue que vous obtenez est destinée à votre point de terminaison principal.
5-Mettez votre point de terminaison principal hors fonction ou supprimez le fichier de surveillance de sorte que Traffic Manager pense qu'il l'est.
6-Attendez jusqu'au terme de la durée de vie DNS du profil Traffic Manager plus 2 minutes supplémentaires. Par exemple, si la durée de vie DNS est de 300 secondes (5 minutes), attendez 7 minutes.
7-Videz votre cache client DNS et demandez une résolution DNS. Dans Windows, vous pouvez vider votre cache DNS avec la commande ipconfig /flushdns émise au niveau d'une commande ou d'une invite de commandes Windows PowerShell.
8-Assurez-vous que l'adresse IP obtenue est destinée à votre point de terminaison secondaire.
9-Répétez le processus en mettant le point de terminaison secondaire hors fonction, puis le troisième, et ainsi de suite. À chaque fois, assurez-vous que la résolution DNS retourne l'adresse IP du prochain point de terminaison de la liste. Une fois que tous les points de terminaison sont hors fonction, vous devez obtenir à nouveau l'adresse IP du point de service principal.

### Pour tester une méthode d'équilibrage de charge par tourniquet (round robin)

1-Maintenez tous les points de terminaison à l'état actif.
2-Utilisez un seul client.
3-Demandez la résolution DNS du domaine de votre entreprise à l'aide de l'outil Nslookup.exe ou d'un utilitaire similaire.
4-Vérifiez que l'adresse IP obtenue figure bien dans votre liste.
5-Videz votre cache client DNS et répétez les étapes 3 et 4 autant de fois que nécessaire. Différentes adresses IP doivent alors être retournées pour chaque point de terminaison. Ensuite, le processus se répète.

### Pour tester une méthode d'équilibrage de charge de performance

Pour tester efficacement une méthode d'équilibrage de charge de performance, vous devez avoir des clients situés dans différentes parties du monde. Vous pouvez créer des clients dans Azure qui tenteront d'appeler vos services via le nom de domaine de votre entreprise. Sinon, si votre entreprise est d'envergure mondiale, vous pouvez vous connecter à distance à des clients situés dans d'autres parties du monde et effectuer les tests à partir de ces clients.

Vous trouverez différents services d'exploration (dig) et de recherche DNS gratuits sur Internet. Certains d'entre eux permettent de contrôler la résolution de noms DNS à partir de différents emplacements. Effectuez une recherche sur " Recherche DNS " pour obtenir des exemples. Vous pouvez aussi utiliser une solution tierce, telle que Gomez ou Keynote, pour vérifier que vos profils répartissent le trafic comme prévu.

## Voir aussi

[À propos des méthodes d'équilibrage de charge de Traffic Manager](about-traffic-manager-balancing-methods.md)
[Tâches de Configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)
[Traffic Manager](traffic-manager.md)

<!--HONumber=49-->