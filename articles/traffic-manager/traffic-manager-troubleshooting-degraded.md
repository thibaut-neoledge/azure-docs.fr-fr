<properties
   pageTitle="Résolution des problèmes liés à l’état détérioré d’Azure Traffic Manager"
   description="Comment résoudre les profils Traffic Manager lorsque l’état est affiché comme dégradé."
   services="traffic-manager"
   documentationCenter=""
   authors="kwill-MSFT"
   manager="adinah"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="joaoma" />
# Résolution des problèmes liés à l’état détérioré d’Azure Traffic Manager
Cette page décrit comment résoudre le problème du profil Azure Traffic Manager qui affiche un état détérioré et fournit quelques points importants à connaître sur les sondes du gestionnaire de trafic.


Vous avez configuré un profil Traffic Manager pointant vers certains de vos services hébergés .cloudapp.net et après quelques secondes, l’état s’affiche comme dégradé.

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Si vous accédez à l’onglet Points de terminaison du profil, vous pouvez voir un ou plusieurs points de terminaison avec l’état hors connexion :

![hors connexion](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Remarques importantes sur la détection de Traffic Manager

- Traffic Manager considère uniquement un point de terminaison comme étant en ligne si la sonde obtient en retour 200 du chemin d’accès de la sonde.
- Une redirection 30x (ou toute réponse autre que 200) échoue, même si l’URL de redirection renvoie 200.

- Pour les sondes HTTPs, les erreurs de certificat sont ignorées.
 
- Le contenu réel du chemin d’accès de la sonde n’importe pas, aussi longtemps que la valeur 200 est retournée. Une technique courante si le contenu du site Web réel ne retourne pas 200 (autrement dit, si les pages ASP redirigent vers une page de connexion ACS ou autre URL CNAME) consiste à définir le chemin d’accès avec « /favicon.ico » ou valeur similaire.
 
- La meilleure pratique consiste à définir le chemin d’accès de la sonde vers un élément qui possède une logique suffisante pour déterminer si le site fonctionne ou est à l’arrêt. Dans l’exemple ci-dessus, en définissant « / favicon.ico » comme chemin d’accès, vous testez uniquement si w3wp.exe répond, mais pas si votre site web est sain. Une meilleure option consisterait à définir un chemin d’accès tel que « / Probe.aspx » et, dans Probe.aspx, à inclure assez de logique pour déterminer si votre site est sain (autrement dit, vérifier les compteurs de performance pour vous assurer que vous n’êtes pas à 100 % de l’UC ou que vous ne recevez pas un grand nombre de demandes ayant échoué, tenter d’accéder aux ressources telles que l’état de session ou de la base de données pour vérifier que la logique de l’application fonctionne, etc.).
 
- Si tous les points de terminaison d’un profil sont dégradés Traffic Manager traite tous les points de terminaison comme sains et acheminent le trafic vers tous les points de terminaison. Il s’agit de vous assurer que tout problème potentiel avec le mécanisme de détection se traduisant par des sondes ayant échoué de manière incorrecte ne provoque pas une interruption complète de votre service.

  

## Résolution de problèmes

L’outil wget permet de résoudre les problèmes d’échecs de sonde de Traffic Manager. Vous pouvez obtenir les fichiers binaires et le package de dépendances à partir de [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Notez que vous pouvez utiliser d’autres programmes tels que Fiddler ou curl à la place de wget : en gros, vous avez uniquement besoin d’un programme qui affiche la réponse HTTP brute.

Une fois que vous avez installé wget, accédez à une invite de commandes et exécutez wget sur l’URL + le port de la sonde et le chemin d’accès configuré dans Traffic Manager. Pour cet exemple, ce serait http://watestsdp2008r2.cloudapp.net:80/Probe.

![résolution des problèmes](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

Utilisation de Wget :

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

Notez que wget indique que l’URL a renvoyé une redirection 301 à http://watestsdp2008r2.cloudapp.net/Default.aspx. Comme la section ci-dessus « Remarques importantes sur la détection de Traffic Manager » nous l’a appris, une redirection 30x est considérée comme un échec par la détection Traffic Manager et la sonde rapporte un état hors connexion. À ce stade, il est très simple de vérifier la configuration du site web et de s’assurer qu’une réponse 200 est retournée par le chemin d’accès /Probe (ou de reconfigurer la sonde Traffic Manager pour pointer vers un chemin d’accès qui retournera 200).

 

Si votre sonde utilise le protocole HTTPs, vous devez ajouter le paramètre « --no-check-certificate » à wget afin qu’il ignore l’incompatibilité du certificat sur l’URL cloudapp.net.


## Étapes suivantes


[À propos des méthodes d’équilibrage de charge de Traffic Manager](traffic-manager-load-balancing-methods.md)

[Vue d’ensemble de Traffic Manager](../traffic-manmager-overview.md)

[Services Cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Applets de commande Azure Traffic Manager](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=July15_HO2-->