<properties 
   pageTitle="Test des paramètres de Traffic Manager | Microsoft Azure"
   description="Cet article vous aidera à tester les paramètres de Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# Tester vos paramètres de Traffic Manager

La meilleure façon de tester les paramètres de Traffic Manager consiste à configurer des clients, puis à mettre hors fonction un à un les services cloud et sites web de votre profil. Voici quelques conseils relatifs au test de votre profil Traffic Manager :

## Étapes de test de base

- **Définissez une durée de vie DNS très courte**, afin que les modifications se répercutent rapidement, en 30 secondes, par exemple.
- **Veillez à connaître l’adresse IP de tous vos services cloud et sites Web Microsoft Azure** contenus dans le profil que vous testez.
- **Utilisez des outils permettant de résoudre un nom DNS en adresse IP** et d'afficher cette adresse. Vous devez vérifier que votre nom de domaine d’entreprise est résolu en adresses IP des points de terminaison de votre profil. Ils doivent être résolus de manière cohérente avec la méthode de routage du trafic utilisée pour votre profil Traffic Manager. Si vous possédez un ordinateur exécutant Windows, vous pouvez utiliser l’outil Nslookup.exe à partir d’une commande ou d’une invite de commandes Windows PowerShell. D’autres outils publics permettant d’obtenir les adresses IP sont également disponibles sur Internet.

### Contrôle d’un profil Traffic Manager à l’aide de nslookup

1. Ouvrez une commande ou une invite de commandes Windows PowerShell en tant qu’administrateur.
2. Entrez `ipconfig /flushdns` pour vider le cache de résolution DNS.
3. Saisissez `nslookup <your Traffic Manager domain name>`. Par exemple, la commande suivante vérifie le nom de domaine présentant le préfixe *myapp.contoso*: nslookup myapp.contoso.trafficmanager.net. Généralement, le résultat se présente comme suit :
   - le nom DNS et l’adresse IP du serveur DNS utilisé pour résoudre le nom de domaine Traffic Manager ;
   - le nom de domaine Traffic Manager saisi dans la ligne de commande après « nslookup » et l’adresse IP correspondant au domaine Traffic Manager. La deuxième adresse IP est celle à vérifier. Elle doit correspondre à l’adresse IP virtuelle (VIP) publique de l’un des services cloud ou sites web du profil Traffic Manager testé.

## Test des méthodes de routage du trafic

### Pour tester une méthode de routage du trafic par basculement

1. Laissez tous les points de terminaison en fonction.
2. Utilisez un client unique.
3. Exécutez une requête de résolution DNS pour votre nom de domaine d’entreprise à l’aide de l’outil Nslookup.exe ou d’un utilitaire similaire.
4. Vérifiez que l’adresse IP résolue correspond à votre point de terminaison principal.
5. Mettez votre point de terminaison principal hors fonction ou supprimez le fichier de surveillance afin que Traffic Manager pense qu’il l’est.
6. Patientez pendant une période correspondant à la durée de vie (TTL, Time-to-Live) du profil Traffic Manager plus 2 minutes. Par exemple, si la TTL de votre DNS est de 300 secondes (5 minutes), vous devez attendre 7 minutes.
7. Videz le cache de client DNS et exécutez une requête de résolution DNS. Dans Windows, vous pouvez vider votre cache DNS en exécutant la commande ipconfig /flushdns à partir d’une commande ou d’une invite de commandes Windows PowerShell.
8. Vérifiez que l’adresse IP obtenue correspond à votre deuxième point de terminaison.
9. Répétez le processus, en mettant le deuxième point de terminaison hors fonction, puis le troisième, etc. Assurez-vous chaque fois que la résolution DNS renvoie l’adresse IP du point de terminaison suivant dans la liste. Une fois tous les points de terminaison hors fonction, vous devez obtenir à nouveau l’adresse IP du point de terminaison principal.

### Pour tester une méthode de routage du trafic en tourniquet (round robin)

1. Laissez tous les points de terminaison en fonction.
2. Utilisez un client unique.
3. Exécutez une requête de résolution DNS pour votre domaine d’entreprise à l’aide de l’outil Nslookup.exe ou d’un utilitaire similaire.
4. Vérifiez que l’adresse IP obtenue figure bien dans votre liste.
5. Videz votre cache de client DNS et répétez les étapes 3 et 4 plusieurs fois. Vous devriez alors obtenir différentes adresses IP pour chacun de vos points de terminaison. Ensuite, le processus se répète.

### Pour tester une méthode de routage du trafic basé sur les performances

Pour tester efficacement une méthode de routage du trafic basé sur les performances, vous devez disposer de plusieurs clients répartis en différents emplacements dans le monde. Vous pouvez en créer dans Azure, afin qu’ils tentent d’appeler vos services via votre nom de domaine d’entreprise. Si votre entreprise est implantée à l’international, vous pouvez également vous connecter à distance à des clients dans d’autres régions du monde et procéder au test depuis ces clients.

Vous trouverez différents services de recherche et d’obtention de DNS gratuits sur Internet. Certains d’entre eux permettent de contrôler la résolution de noms DNS à partir de différents emplacements. Faites une recherche sur « Recherche DNS » pour obtenir des exemples. Vous pouvez également utiliser une solution tierce, telle que Gomez ou Keynote, pour vérifier que vos profils répartissent le trafic comme prévu.

## Étapes suivantes

[Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)




 

<!---HONumber=AcomDC_1210_2015-->