<properties 
	pageTitle="Comment configurer un environnement App Service" 
	description="Configuration, gestion et surveillance d'environnements App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2016" 
	ms.author="ccompy"/>


# Configuration d'un environnement App Service #

## Vue d'ensemble ##

Globalement, un environnement App Service se compose de plusieurs composants principaux :

- Ressources de calcul s'exécutant dans le service hébergé d'environnement Azure App
- Stockage
- Base de données
- Réseau virtuel « v1 » classique avec au moins un sous-réseau
- Sous-réseau sur lequel s'exécute le service hébergé d'environnement Azure App

### Ressources de calcul

Les ressources de calcul sont utilisées pour vos 4 pools de ressources. Chaque environnement App Service possède un ensemble de serveurs frontaux et 3 pools de travail possibles. Vous n’avez pas besoin d’utiliser les 3 pools de travail et, si vous le souhaitez, vous pouvez n’en utiliser qu’un ou deux. Les pools de ressources (des serveurs frontaux et Workers) ne sont pas directement accessibles par les clients. Vous ne pouvez pas effectuer d’opération de protocole RDP vers eux, modifier leur approvisionnement ou agir en tant qu’administrateur, mais vous pouvez définir leur quantité ainsi que leur taille. Dans un environnement App Service, vous disposez de 4 options de taille (P1 à P4). Vous trouverez des informations détaillées sur ces tailles et leur tarification ici [Service d'application Tarification](../app-service/app-service-value-prop-what-is.md). Il ne peut y avoir qu’une seule opération de mise à l’échelle en cours à la fois.

**Serveurs frontaux** Les serveurs frontaux sont les points de terminaison HTTP/HTTPS pour vos applications maintenues dans votre environnement App Service. Les charges de travail ne sont pas exécutées dans les serveurs frontaux.

- Un environnement App Service commence avec (2) P2, ce qui est suffisant pour les charges de travail de développement et de test et les charges de travail de production de bas niveau. Les P3 sont fortement recommandés pour des charges de travail de production moyennes et lourdes.
- Il est recommandé d’avoir au moins 4 P3 pour les charges de travail moyennes et lourdes, afin de garantir l’exécution d’un nombre suffisant de serveurs frontaux pendant la maintenance planifiée. La maintenance planifiée interrompt (1) serveur frontal à la fois, ce qui réduit la capacité globale disponible des serveurs frontaux lors de la maintenance planifiée.
- L’ajout instantané d’une nouvelle instance de serveur frontal n’est pas possible. L’approvisionnement de cette dernière peut prendre entre 2 à 3 heures.
- Pour un ajustement optimal de la mise à l’échelle, les clients doivent analyser le pourcentage UC, le pourcentage de mémoire ainsi que les mesures des demandes actives pour le pool frontal. Si le pourcentage UC ou de mémoire est supérieur à 70 % lorsque vous exécutez des P3, ajoutez davantage de serveurs frontaux. Si la moyenne des demandes actives oscille entre 15 000 et 20 000 demandes par serveur frontal, vous devez également ajouter davantage de serveurs frontaux. L’objectif est de maintenir le pourcentage UC et de mémoire en dessous de 70 % et le nombre moyen des demandes actives en dessous de 15 000 par serveur frontal lorsque vous exécutez des P3.  

**Workers** Les Workers sont le lieu d’exécution de vos applications. Lorsque vous faites monter en puissance vos plans App Service, cette opération utilise des Workers dans le pool de travail associé.

- L’ajout instantané de Workers n’est pas possible. L’approvisionnement de ces derniers peut prendre entre 2 à 3 heures, quel que soit le nombre de Workers ajoutés.
- La mise à l’échelle d’une ressource de calcul pour un pool quelconque prendra 2 à 3 heures par domaine de mise à jour. Un environnement App Service contient 20 domaines de mise à jour. Si vous mettez à l’échelle la taille de calcul d’un pool de travail contenant 10 instances, cette opération peut prendre entre 20 et 30 heures. 
- Si vous modifiez la taille des ressources de calcul utilisées dans un pool de travail, vous pouvez provoquer des démarrages à froid des applications en cours d’exécution dans ce pool de travail.

La façon la plus rapide de modifier la taille des ressources de calcul d’un pool de travail qui n’exécute pas d’applications est de :

- descendre en puissance le nombre d’instances à 0. La libération de vos instances prendra environ 30 minutes ;
- sélectionner la nouvelle taille de calcul et le nombre d’instances. À partir de là, l’opération prendra entre 2 à 3 heures.

Si vos applications nécessitent une plus grande taille de ressources de calcul, les étapes précédentes ne vous concernent pas. Au lieu de modifier la taille du pool de travail qui héberge ces applications, vous pouvez remplir un autre pool de travail avec des Workers de la taille souhaitée, puis déplacer vos applications vers ce pool.

- Créez les instances supplémentaires de la taille de calcul nécessaire dans un autre pool de travail. Cette opération prendra entre 2 à 3 heures.
- Réaffectez vos plans App Service hébergeant les applications qui nécessitent une plus grande taille au pool de travail nouvellement configuré. Il s’agit d’une opération rapide qui devrait prendre moins d’une minute.  
- Descendez en puissance le premier pool de travail si ces instances inutilisées ne vous servent plus. Cette opération prend environ 30 minutes.

**Mise à l’échelle automatique** La mise à l’échelle automatique constitue l’un des outils pouvant vous aider à gérer la consommation des ressources de calcul ; elle s’applique aux pools frontaux ou aux pools de travail. Vous pouvez effectuer des opérations comme augmenter le nombre d’instances d’un pool quelconque le matin, puis le réduire le soir, voire ajouter des instances lorsque le nombre de Workers disponibles dans un pool de travail descend sous un certain seuil. Si vous souhaitez définir des règles de mise à l’échelle automatique en vous basant sur des mesures de pool de ressources de calcul, gardez en tête le temps d’approvisionnement nécessaire. Pour des informations concernant la mise à l’échelle automatique des environnements App Service, consultez [Mise à l’échelle automatique et environnement App Service][ASEAutoscale].

### Storage

Chaque ASE est configuré avec 500 Go de stockage. Cet espace est utilisé par toutes les applications dans l’ASE. Cet espace de stockage est une partie de l’ASE ; actuellement, aucun basculement pour utiliser l’espace de stockage du client n’est possible. Si vous ajustez le routage ou la sécurité de votre réseau virtuel, vous devez autoriser l’accès à Azure Storage ; sinon, l’ASE ne fonctionnera pas.

### Base de données

La base de données conserve les informations qui définissent l’environnement, ainsi que des détails sur les applications exécutées. Il s’agit également d’une partie de l’abonnement Azure et ce n’est pas un élément pour lequel les clients peuvent effectuer des manipulations directes. Si vous ajustez le routage ou la sécurité de votre réseau virtuel, vous devez autoriser l’accès à SQL Azure ; sinon, l’ASE ne fonctionnera pas.

### Réseau

Le réseau virtuel qui est utilisé avec votre ASE peut être un réseau que vous avez créé lors de la création de l’ASE ou un réseau que vous aviez créé au préalable. Si vous souhaitez que votre réseau virtuel figure dans un groupe de ressources distinct de celui utilisé pour votre ASE, vous devez créer votre réseau virtuel séparément du flux de création de l’ASE. Si vous créez le sous-réseau lors de la création de l’environnement App Service, cela forcera l’ASE à être dans le même groupe de ressources que le réseau virtuel.

Un réseau virtuel contient quelques restrictions utilisées pour un environnement App Service :

- Actuellement, seuls les réseaux virtuels « classiques » V1 sont pris en charge
- le réseau virtuel doit être un réseau virtuel régional.
- Les réseaux virtuels utilisés pour héberger un environnement App Service doivent utiliser des adresses RFC1918 (c’est-à-dire, des adresses privées)
- Un sous-réseau avec 8 adresses ou plus où est déployé l’ASE doit exister
- Lorsqu’un sous-réseau est utilisé pour héberger un ASE, la plage d’adresses du sous-réseau ne peut être modifiée. Pour cette raison, il est recommandé que le sous-réseau contienne au moins 64 adresses pour la prise en charge d’ASE supplémentaires potentiels 

Contrairement au service hébergé qui contient l’ASE, le [réseau virtuel][virtualnetwork] et le sous-réseau sont tous les deux contrôlés par l’utilisateur. L’administration de votre réseau virtuel s’effectue via l’interface utilisateur de réseau virtuel ou Powershell.

Étant donné que cette fonctionnalité place Azure App Service sur votre réseau virtuel, vos applications hébergées dans votre environnement App Service (ASE) peuvent désormais accéder aux ressources rendues disponibles directement via des VPN ExpressRoute ou site à site. Les applications de vos environnements App Service ne nécessitent pas de fonctionnalités de mise en réseau supplémentaires pour accéder aux ressources disponibles sur le réseau virtuel qui héberge votre environnement App Service. Cela signifie que vous n’avez pas besoin d’utiliser l’intégration de réseau virtuel ou les connexions hybrides pour accéder aux ressources dans ou connectées à votre réseau virtuel. Vous pouvez toujours utiliser ces deux fonctionnalités pour accéder aux ressources dans les réseaux qui ne sont pas connectés à votre réseau virtuel. Par exemple, vous pouvez utiliser l’intégration de réseau virtuel pour vous intégrer à un réseau virtuel qui figure dans votre abonnement mais qui n’est pas connecté au réseau virtuel dans lequel se trouve votre ASE. Vous pouvez toujours également utiliser des connexions hybrides pour accéder aux ressources dans d’autres réseaux, comme vous le faites normalement.

Si votre réseau virtuel est configuré avec un VPN ExpressRoute, vous devez être informé de certains des besoins de routage d’un ASE. Certaines configurations d’itinéraire défini par l’utilisateur ne sont pas compatibles avec un ASE. Pour plus de détails concernant l’exécution d’un ASE dans un réseau virtuel avec ExpressRoute, consultez le document [Exécution d’un environnement App Service dans un réseau virtuel avec ExpressRoute][ExpressRoute].

Vous pouvez désormais également contrôler l’accès à vos applications à l’aide de groupes de sécurité réseau. Cette fonctionnalité vous permet de verrouiller votre environnement App Service pour autoriser uniquement les adresses IP auxquelles vous souhaitez le restreindre. Pour plus d'informations sur la façon de procéder, consultez le document disponible ici [Comment contrôler le trafic entrant dans un environnement App Service](app-service-app-service-environment-control-inbound-traffic.md).

## Portail

L’interface utilisateur permettant de gérer et surveiller votre environnement App Service est disponible à partir du portail Azure. Si vous possédez un ASE, il est possible que vous voyiez le symbole App Service dans votre barre latérale. Ce symbole est utilisé pour représenter les environnements App Service dans le portail Azure.

![][1]

Vous pouvez utiliser l’icône ou sélectionner le chevron (symbole « supérieur à ») au bas de la barre latérale et sélectionner les environnements App Service. Les deux actions produisent le même résultat, à savoir, ouvrir l’interface utilisateur qui répertorie l’ensemble de vos environnements App Service. La sélection de l’un des ASE répertoriés ouvre l’interface utilisateur utilisée pour le surveiller et le gérer.

![][2]

Ce premier panneau présente certaines propriétés de votre ASE avec un graphique de mesures par pool de ressources. Certaines des propriétés affichées dans le bloc Essentiels sont également des liens hypertexte qui ouvrent le panneau associé. Par exemple, vous pouvez sélectionner le nom de réseau virtuel pour ouvrir l’interface utilisateur associée au réseau virtuel dans lequel votre ASE est exécuté. Les plans App Service et les applications ouvrent des panneaux qui répertorient les éléments figurant dans votre ASE.

### Analyse

Les graphiques permettent de voir les différentes mesures de performances dans chaque pool de ressources. Analysez l’utilisation moyenne de l’UC et de la mémoire pour le pool frontal. Analysez la quantité utilisée et la quantité disponible pour les pools de travail. Plusieurs plans App Service peuvent utiliser les Workers dans un pool de travail. La charge de travail n’est pas distribuée de la même manière que pour les pools frontaux. L’analyse de l’utilisation de l’UC et de la mémoire ne fournit donc pas des informations pertinentes. Il est plus important de suivre le nombre de Workers que vous avez utilisés et qui sont disponibles, en particulier si vous gérez ce système pour d’autres utilisateurs.

Toutes les mesures qui peuvent être suivies dans les graphiques peuvent également servir à configurer des alertes. La configuration d’alertes fonctionne de la même manière qu’ailleurs dans App Service. Vous pouvez définir une alerte à partir de l’interface utilisateur Alertes ou dans toute interface utilisateur de mesures en cliquant sur Ajouter une alerte.
 
![][3]

Les mesures dont nous venons de parler sont les mesures de l’environnement App Service. Des mesures sont également disponibles au niveau du plan App Service. C’est là que la surveillance de l’utilisation de l’UC et de la mémoire se révèle intéressante. Dans un ASE, tous les ASP sont des ASP dédiés. Cela signifie que les seules applications qui sont exécutées sur les hôtes alloués à cet ASP sont les applications de cet ASP. Pour afficher des détails sur votre ASP, affichez simplement votre ASP à partir d’une liste quelconque dans l’interface utilisateur de l’ASE, voire à partir des plans App Service qui les répertorient tous.

### Paramètres

Le panneau de l’ASE contient une section Paramètres. Cette dernière comprend plusieurs fonctionnalités importantes

**Paramètres > Propriétés** Le panneau Paramètres s’ouvre automatiquement lorsque vous affichez le panneau de votre ASE. Les Propriétés se trouvent dans la partie supérieure. Ici, plusieurs éléments sont redondants avec ce que vous voyez dans le bloc Essentiels, mais deux propriétés sont très utiles : l’adresse IP virtuelle et l’adresse IP sortante.

![][4]

**Paramètres > Adresses IP** Une adresse IP SSL est nécessaire lorsque vous créez une application IP SSL dans votre environnement App Service. Pour ce faire, votre ASE doit posséder des adresses IP SSL qui peuvent être allouées. Lorsqu’un ASE est créé, il dispose d’une adresse IP SSL unique à cette fin, mais vous pouvez en ajouter d’autres. Ces adresses IP SSL supplémentaires sont payantes. Consultez les prix sur la page [Tarification de App Service][AppServicePricing], dans la rubrique Connexions SSL. Le supplément de prix correspond au coût IP SSL.

**Paramètres > Pool frontal / Pools de travail** Chacun de ces panneaux de pools de ressources offre la possibilité d’afficher des informations uniquement sur ce pool de ressources, en plus de proposer des contrôles pour mettre entièrement à l’échelle ce pool de ressources.

Le panneau de base pour chaque pool de ressources fournit un graphique avec des mesures pour ce pool de ressources. À l’instar des graphiques du panneau de l’ASE, vous pouvez entrer dans le graphique et configurer les alertes de votre choix. La configuration d’une alerte à partir du panneau de l’ASE pour un pool de ressources spécifique produit le même résultat que d’effectuer l’opération à partir du pool de ressources. Dans le panneau Paramètres du pool de travail, vous avez accès à la liste de l’ensemble des applications ou plans App Service qui s’exécutent dans ce pool de travail.

![][5]

### Fonctionnalités de mise à l’échelle du portail  

Il existe trois opérations de mise à l’échelle :

- Modifier le nombre d’adresses IP dans l’ASE qui sont disponibles pour l’utilisation IP SSL
- Modifier la taille de la ressource de calcul utilisée dans un pool de ressources
- Modifier le nombre de ressources de calcul utilisées dans un pool de ressources manuellement ou via la mise à l’échelle automatique

Dans le portail, il existe trois façons de contrôler le nombre de serveurs dont vous disposez dans vos pools de ressources

- Opération de mise à l’échelle à partir du panneau principal de l’ASE dans la partie supérieure Vous pouvez modifier la configuration de mise à l’échelle du pool frontal et du pool de travail de plusieurs façons ; toutes les modifications seront appliquées en une seule opération.
- Opération de mise à l’échelle manuelle à partir du panneau Mise à l’échelle du pool de ressources individuel, sous Paramètres
- Mise à l’échelle automatique configurée à partir du panneau Mise à l’échelle du pool de ressources individuel

Pour utiliser l’opération de mise à l’échelle sur le panneau de l’ASE, faites glisser le curseur vers la quantité souhaitée et enregistrez. Cette interface utilisateur prend également en charge la modification de la taille.

![][6]

Pour utiliser les fonctionnalités de mise à l’échelle automatique ou manuelle dans un pool de ressources spécifique, accédez à *Paramètres > Pool frontal / Pools de travail*, selon le cas, et ouvrez le pool que vous souhaitez modifier. Accédez à *Paramètres > Augmenter la taille des instances ou Paramètres > Monter en puissance*. Le panneau *Augmenter la taille des instances* vous permet de contrôler la qualité des instances. *Monter en puissance* vous permet de contrôler la taille de la ressource.

![][7]

## Considérations relatives à la tolérance de pannes

Il est possible de configurer un environnement App Service pour utiliser jusqu'à 55 ressources de calcul au total. De ces 55 ressources de calcul, seules 50 peuvent être utilisées pour héberger des charges de travail. Il y a deux raisons à cela. Il existe au minimum 2 ressources de calcul frontales, ce qui en laisse 53 au maximum pour prendre en charge d'allocation des pools de travaux. Pour fournir une tolérance de panne, une ressource de calcul supplémentaire doit être allouée en respectant les règles suivantes :

- Chaque pool de travail nécessite au moins une ressource de calcul supplémentaire qui n’est pas disponible pour qu’une charge de travail lui soit affectée.
- Lorsque la quantité de ressources de calcul dans un pool de travail dépasse une certaine valeur, une autre ressource de calcul est requise pour la tolérance de panne. Cela n’est pas le cas dans le pool de serveurs frontaux.

Dans tout un pool de travaux unique, les conditions requises pour la tolérance de pannes sont que pour une valeur donnée de X ressources affectées à un pool de travaux :

- si X est compris entre 2 et 20, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-1
- si X est compris entre 21 et 40, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-2
- si X est compris entre 41 et 53, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-3

L’encombrement minimal comprend 2 serveurs frontaux et 2 Workers. Voici quelques exemples pour clarifier les déclarations ci-dessus.

- Si vous avez 30 Workers dans un pool unique, 28 d’entre eux peuvent servir à héberger les charges de travail. 
- Si vous avez 2 Workers dans un pool unique, 1 peut servir à héberger les charges de travail.
- Si vous avez 20 Workers dans un pool unique, 19 peuvent servir à héberger les charges de travail.  
- Si vous avez 21 Workers dans un pool unique, seuls 19 peuvent servir à héberger les charges de travail.  

L’aspect tolérance de panne est important, et vous devez le garder à l’esprit à mesure que votre mise à l’échelle dépasse certains seuils. Si vous souhaitez augmenter la capacité au-delà de 20 instances, alors passez à 22 instances ou plus, car un nombre de 21 instances n’ajoute pas plus de capacité. Cette remarque est également valable au-delà de 40 instances : le nombre suivant qui ajoute de la capacité est 42.

## Suppression d'un environnement App Service ##

Si vous voulez supprimer un environnement App Service, utilisez simplement l'action de suppression en haut du volet Environnement App Service. Lorsque vous effectuez cette opération, vous êtes invité à entrer le nom de votre environnement App Service pour confirmer que vous souhaitez vraiment cela. REMARQUE : lorsque vous supprimez un environnement App Service, vous supprimez également tout son contenu.

![][9]

## Prise en main

Pour prendre en main les environnements App Service, consultez [Comment créer un environnement App Service](app-service-web-how-to-create-an-app-service-environment.md).

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0413_2016-->