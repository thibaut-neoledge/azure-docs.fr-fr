<properties
   pageTitle="Test de vos paramètres de Traffic Manager"
   description="Cet article vous aide à tester les paramètres de Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="joaoma" />

# Test des paramètres de Traffic Manager

La meilleure façon de tester les paramètres de Traffic Manager consiste à configurer des clients, puis à mettre hors fonction un à un les services cloud et sites web de votre profil. Voici quelques conseils relatifs au test de votre profil Traffic Manager :

## Étapes de test de base

- **Définissez une durée de vie DNS très courte**, afin que les modifications se répercutent rapidement, en 30 secondes, par exemple.

- **Veillez à connaître l’adresse IP de tous vos services cloud et sites web Azure** contenus dans le profil que vous testez.

- **Utilisez des outils permettant de résoudre un nom DNS en adresse IP** et d’afficher cette adresse. Vous devez vérifier que votre nom de domaine d’entreprise est résolu en adresses IP des points de terminaison de votre profil. Ils doivent être résolus de manière cohérente avec la méthode d’équilibrage de charge utilisée pour votre profil Traffic Manager. Si vous possédez un ordinateur exécutant Windows, vous pouvez utiliser l’outil Nslookup.exe à partir d’une commande ou d’une invite de commandes Windows PowerShell. D’autres outils publics permettant d’obtenir les adresses IP sont également disponibles sur Internet.

### Contrôle d’un profil Traffic Manager à l’aide de nslookup

1-Ouvrez une commande ou une invite de commandes Windows PowerShell en tant qu'administrateur.

2-Tapez `ipconfig /flushdns` pour vider le cache de résolution DNS.

3-Tapez `nslookup <your Traffic Manager domain name>`. Par exemple, la commande suivante vérifie le nom de domaine présentant le préfixe *myapp.contoso* nslookup myapp.contoso.trafficmanager.net. Généralement, le résultat se présente comme suit :
- le nom DNS et l’adresse IP du serveur DNS utilisé pour résoudre le nom de domaine Traffic Manager ;
- le nom de domaine Traffic Manager saisi dans la ligne de commande après « nslookup » et l’adresse IP correspondant au domaine Traffic Manager. La deuxième adresse IP est celle à vérifier. Elle doit correspondre à l’adresse IP virtuelle (VIP) publique de l’un des services cloud ou sites web du profil Traffic Manager testé.

## Test des méthodes d’équilibrage de charge


### Test d’une méthode d’équilibrage de charge de basculement

1-Maintenez tous les points de terminaison à l'état actif. 2–Utilisez un seul client. 3-Demandez la résolution DNS du nom de domaine de votre entreprise à l'aide de l'outil Nslookup.exe ou d'un utilitaire similaire. 4-Vérifiez que l’adresse IP de résolution obtenue correspond à votre point de terminaison principal 5-Mettez votre point de terminaison principal hors fonction ou supprimez le fichier de surveillance afin que Traffic Manager pense qu’il l’est. 6-Attendez jusqu'au terme de la durée de vie DNS du profil Traffic Manager plus 2 minutes supplémentaires. Par exemple, si la TTL de votre DNS est de 300 secondes (5 minutes), vous devez attendre 7 minutes. 7-Videz votre cache client DNS et demandez une résolution DNS. Dans Windows, vous pouvez vider votre cache DNS en exécutant la commande ipconfig /flushdns à partir d’une commande ou d’une invite de commandes Windows PowerShell. 8–Assurez-vous que l'adresse IP obtenue est destinée à votre point de terminaison secondaire. 9–Répétez le processus en mettant le point de terminaison secondaire hors fonction, puis le troisième, et ainsi de suite. Assurez-vous chaque fois que la résolution DNS renvoie l’adresse IP du point de terminaison suivant dans la liste. Une fois tous les points de terminaison hors fonction, vous devez obtenir à nouveau l’adresse IP du point de terminaison principal.

### Test d’une méthode d’équilibrage de charge tourniquet (round robin)

1-Maintenez tous les points de terminaison à l'état actif. 2–Utilisez un seul client. 3-Demandez la résolution DNS du domaine de votre entreprise à l'aide de l'outil Nslookup.exe ou d'un utilitaire similaire. 4-Vérifiez que l'adresse IP obtenue figure bien dans votre liste. 5-Videz votre cache client DNS et répétez les étapes 3 et 4 autant de fois que nécessaire. Vous devriez alors obtenir différentes adresses IP pour chacun de vos points de terminaison. Ensuite, le processus se répète.

### Test d’une méthode d’équilibrage de charge des performances

Pour tester efficacement une méthode d’équilibrage de charge des performances, vous devez disposer de plusieurs clients répartis en différents emplacements dans le monde. Vous pouvez en créer dans Azure, afin qu’ils tentent d’appeler vos services via votre nom de domaine d’entreprise. Si votre entreprise est implantée à l’international, vous pouvez également vous connecter à distance à des clients dans d’autres régions du monde et procéder au test depuis ces clients.

Vous trouverez différents services de recherche et d’obtention de DNS gratuits sur Internet. Certains d’entre eux permettent de contrôler la résolution de noms DNS à partir de différents emplacements. Faites une recherche sur « Recherche DNS » pour obtenir des exemples. Vous pouvez également utiliser une solution tierce, telle que Gomez ou Keynote, pour vérifier que vos profils répartissent le trafic comme prévu.

## Étapes suivantes

[À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-routing-methods.md) [Traffic Manager](traffic-manager-overview.md)
 

<!---HONumber=AcomDC_0615_2016-->