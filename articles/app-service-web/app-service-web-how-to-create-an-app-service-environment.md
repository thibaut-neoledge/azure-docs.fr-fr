<properties 
	pageTitle="Comment créer un environnement App Service" 
	description="Description du flux de création pour les environnements App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016" 
	ms.author="ccompy"/>

# Comment créer un environnement App Service #

Les environnements App Service constituent une option de service Premium d’Azure App Service offrant une fonction de configuration améliorée qui n’est pas disponible dans les clusters mutualisés. La fonctionnalité ASE déploie essentiellement Azure App Service sur le réseau virtuel du client. Pour mieux comprendre les possibilités offertes par les environnements App Service, lisez la documentation [Présentation d'un environnement App Service][WhatisASE].


### Vue d'ensemble ###

Un ASE se compose de ressources frontales et de ressources de calcul de travail. Les ressources frontales servent de points de terminaison HTTP/HTTPS et envoient le trafic vers les travaux, les rôles qui hébergent vos applications.

Pour créer un ASE, les clients doivent entrer les informations suivantes :

- nom de l’ASE
- abonnement à utiliser pour l’ASE
- resource group
- Azure Virtual Network (VNet) avec au moins 8 adresses et un sous-réseau devant être utilisé par l’ASE
- Type de VIP, externe ou interne
- définition du pool de ressources ASE


Il existe quelques détails importants pour chacun de ces éléments.

- Le nom de l'ASE servira dans le sous-domaine de toutes les applications faites dans cet ASE, s’il est configuré avec une adresse VIP externe
- Un ASE avec une adresse externe héberge des applications accessibles via Internet. Un ASE avec une adresse VIP interne utilise un équilibreur de charge interne (ILB)
- Toutes les applications faites dans un ASE se trouveront dans le même abonnement que l'ASE lui-même
- Si vous n'avez pas accès à l'abonnement utilisé pour faire l'ASE, vous ne pouvez pas utiliser l'ASE pour créer des applications
- Les réseaux virtuels utilisés pour héberger un ASE doivent être des réseaux virtuels régionaux. Vous pouvez utiliser les réseaux virtuels classiques ou du Gestionnaire de ressources
- **Le sous-réseau utilisé pour héberger l’ASE ne doit contenir aucune autre ressource de calcul**
- Il ne peut exister qu’un seul ASE dans un sous-réseau
- Les ASE peuvent désormais être déployés dans les réseaux virtuels qui utilisent *soit* des plages d’adresses publiques *soit* des espaces d’adressage RFC1918 (par exemple, des adresses privées). Pour utiliser un réseau virtuel avec une plage d’adresses publiques, vous devez créer le réseau virtuel et le sous-réseau à l’avance, puis sélectionner le sous-réseau dans l’expérience utilisateur de création d’un ASE.


Chaque déploiement d'ASE est un service hébergé qu'Azure gère et tient à jour. Les ressources de calcul qui hébergent les rôles système d'ASE ne sont pas accessibles au client, mais le client gère la quantité d'instances et leur taille.

Il existe deux façons d'accéder à l'interface utilisateur de création d'un ASE. Il est possible de la trouver en recherchant ***environnement App Service *** dans Azure Marketplace ou en accédant à Nouveau -> Web et mobilité.

Si vous voulez associer un groupe de ressources distinct au réseau virtuel, vous devez d’abord créer un réseau virtuel séparément, puis le sélectionner lors de la création de l’ASE. Par ailleurs, si vous voulez créer un sous-réseau dans un réseau virtuel existant lors de la création de l’ASE, ce dernier doit se trouver dans le même groupe de ressources que le réseau virtuel.


### Création rapide ###
L’expérience de création d’un ASE comporte un ensemble de valeurs par défaut qui permettent d’accélérer le processus. Vous pouvez créer un ASE rapidement en entrant simplement un nom pour le déploiement. Un ASE est alors créé dans la région la plus proche avec les éléments suivants :

- Réseau virtuel avec 512 adresses utilisant un espace d’adressage privé RFC1918
- sous-réseau avec 256 adresses
- VIP externe
- pool frontal avec 2 ressources de calcul P2
- pool de travail avec 2 ressources de calcul P1
- adresse IP unique à utiliser pour IP SSL

La taille P2 ou supérieure est requise pour les pools frontaux. Veillez à sélectionner l’abonnement dans lequel se trouvera l’ASE. Les seuls comptes qui peuvent utiliser l'ASE pour héberger le contenu doivent être dans l'abonnement utilisé pour le créer.

![][1]

Le nom spécifié pour l’ASE sera utilisé pour les applications créées dans l’ASE. Si le nom de l’ASE est appsvcenvdemo, le nom du sous-domaine est .*appsvcenvdemo.p.azurewebsites.net*. Par conséquent, si vous créez une application nommée *mytestapp*, elle est adressable à l’adresse *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Vous ne pouvez pas utiliser d’espace blanc dans le nom de votre ASE. Si vous utilisez des caractères en majuscules dans le nom, le nom de domaine sera la version complète de ce nom en minuscules. Si vous utilisez un ILB, le nom de votre ASE n’est pas utilisé dans votre sous-domaine mais explicitement indiqué lors de la création de l’ASE.

Les valeurs par défaut sont très utiles dans un certain nombre de situations, mais vous devrez souvent modifier quelque chose. Les sections suivantes vous guident dans chacune des sections de configuration de l’ASE.


### Réseau virtuel ###
Le processus de création de l’ASE prend en charge la sélection d’un réseau virtuel classique ou du Gestionnaire des ressources ainsi que la création d’un nouveau réseau virtuel classique.

Lorsque vous sélectionnez un réseau virtuel existant, vous constaterez que vos réseaux virtuels classiques et du Gestionnaire des ressources apparaissent ensemble. Les réseaux virtuels classiques sont signalés par le terme Classic en regard de leur emplacement. Si ce terme n’est pas affiché, cela signifie qu’il s’agit d’un réseau virtuel du Gestionnaire de ressources.

![][2]


Si vous utilisez l'interface utilisateur de création de réseau virtuel, vous devez indiquer les éléments suivants :

- Nom du réseau virtuel
- Plage d'adresses du réseau virtuel en notation CIDR
- Emplacement

L’emplacement du réseau virtuel correspond à l’emplacement de l’ASE. N’oubliez pas que ce processus crée un réseau virtuel classique et non un réseau virtuel du Gestionnaire de ressources.

Les ASE peuvent être déployés dans les réseaux virtuels qui utilisent *soit* des plages d’adresses publiques *soit* des espaces d’adressage RFC1918 (par exemple, des adresses privées). Pour utiliser un réseau virtuel avec une plage d’adresses publiques, vous devez créer le sous-réseau à l’avance, puis sélectionner le sous-réseau dans l’expérience utilisateur de création d’un ASE.

Si vous ne sélectionnez pas un réseau virtuel préexistant, vous devez également spécifier un sous-réseau à utiliser ou en créer un. Le sous-réseau doit avoir au moins 8 adresses et ne peut pas contenir d’autres ressources au préalable. La création d’un ASE échoue si vous utilisez un sous-réseau contenant déjà des machines virtuelles allouées.

Une fois votre réseau virtuel spécifié ou sélectionné, vous devez créer ou sélectionner un sous-réseau approprié. Les détails que vous devez fournir sont les suivants :

- Nom du sous-réseau
- Plage de sous-réseau en notation CIDR

La notation CIDR (Classless Inter-Domain Routing) se présente sous la forme d’une adresse IP séparée de la valeur CIDR par une barre oblique, comme ceci : *10.0.0.0/22*. La valeur CIDR représente le nombre de bits de début masqués sur l’adresse IP affichée. Pour faire simple, la valeur CIDR fournit une plage d’adresses IP. Dans cet exemple, 10.0.0.0/22 représente une plage de 1 024 adresses ou comprise entre 10.0.0.0 et 10.0.3.255. Un /23 signifie 512 adresses, et ainsi de suite.

Par ailleurs, si vous voulez créer un sous-réseau dans un réseau virtuel existant, l’ASE doit se trouver dans le même groupe de ressources que le réseau virtuel. Pour conserver votre ASE dans un groupe de ressources distinct de votre réseau virtuel, créez votre réseau et votre sous-réseau séparément avant de créer votre ASE.


#### Adresse VIP externe ou interne ####

Par défaut, la configuration du réseau virtuel est définie avec un type d’adresse VIP externe et 1 adresse IP. Si vous souhaitez utiliser un ILB au lieu d’une adresse VIP externe, accédez à la configuration du réseau virtuel et modifiez le type d’adresse VIP en Interne. Une adresse IP externe est utilisée par défaut. Lorsque vous modifiez le type d’adresse VIP sur Interne, vous devez spécifier votre sous-domaine pour l’ASE. Vous devez accepter quelques compromis si vous utilisez un ILB comme adresse VIP virtuelle pour un ASE. Pour en savoir plus, consultez [Utilisation d’un équilibreur de charge interne avec un environnement App Service][ILBASE].

![][4]

### Pools de ressources de calcul ###

Lors de la création de l’ASE, vous pouvez définir le nombre de ressources allouées à chaque pool de ressources, ainsi que la taille de ceux-ci. Vous pouvez définir la taille de vos pools de ressources au moment de la création de l’ASE, mais également les ajuster ultérieurement à l’aide des options de mise à l’échelle manuelle ou automatique.

Comme indiqué précédemment, un environnement App Service se compose de serveurs frontaux et de travaux. Les serveurs frontaux gèrent la charge de connexion d'application et les travaux exécutent le code d'application. Les serveurs frontaux sont gérés dans un pool de ressources de calcul dédiées. Les travaux, quant à eux, sont gérés dans 3 pools de ressources de calcul distincts nommés

- Pool de travaux 1
- Pool de travaux 2
- Pool de travaux 3

Si vous avez un grand nombre de demandes pour des applications web simples, vous adapterez probablement la taille de vos serveurs frontaux et réduirez le nombre de travaux. Si vous avez des applications web utilisant beaucoup de mémoire ou le processeur avec un faible trafic, vous n'aurez pas besoin de nombreux serveurs frontaux, mais probablement de travaux plus nombreux ou plus volumineux.

![][3]

Quelle que soit la taille des ressources de calcul, l'encombrement minimal comprend 2 serveurs frontaux et 2 travaux. Il est possible de configurer un environnement App Service pour utiliser jusqu’à 55 ressources de calcul au total. De ces 55 ressources de calcul, seules 50 peuvent être utilisées pour héberger des charges de travail. Il y a deux raisons à cela. Il existe au minimum 2 ressources de calcul frontales, ce qui en laisse 53 au maximum pour prendre en charge d'allocation des pools de travaux. Pour fournir une tolérance de panne, une ressource de calcul supplémentaire doit être allouée en respectant les règles suivantes :

- Chaque pool de travaux nécessite au moins une ressource de calcul supplémentaire qui ne peut pas être affectée à la charge de travail.
- Lorsque la quantité de ressources de calcul d'un pool dépasse une certaine valeur, une autre ressource de calcul est requise.

Dans tout un pool de travaux unique, les conditions requises pour la tolérance de pannes sont que pour une valeur donnée de X ressources affectées à un pool de travaux :

- si X est compris entre 2 et 20, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-1
- si X est compris entre 21 et 40, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-2
- si X est compris entre 41 et 53, la quantité de ressources de calcul utilisables que vous pouvez utiliser pour les charges de travail est X-3

En plus de pouvoir gérer la quantité de ressources de calcul que vous pouvez affecter à un pool donné, vous avez également le contrôle de la taille. Avec les environnements App Service, vous avez le choix entre 4 tailles différentes libellées P1 à P4. Vous trouverez des informations détaillées sur ces tailles et leur tarification ici [Service d'application Tarification][AppServicePricing]. Les tailles de ressources de calcul P1 à P3 sont identiques à ce qui est disponible dans les environnements mutualisés. La ressource de calcul P4 offre 8 cœurs avec 14 Go de RAM et est uniquement disponible dans un environnement App Service.

La tarification pour les environnements App Service est fonction des ressources de calcul affectées. Vous payez pour les ressources de calcul allouées à votre environnement App Service, qu'elles hébergent, ou non, des charges de travail.

Par défaut, un ASE est fourni avec 1 adresse IP disponible pour IP SSL. Si vous savez que vous en aurez besoin de plus, vous pouvez le spécifier ici ou la gérer une fois la création effectuée.
  
### Après la création d'un environnement App Service ###

Après la création d'un ASE, vous pouvez ajuster les éléments suivants :

- Quantité de serveurs frontaux (minimum : 2)
- Quantité de travaux (minimum : 2)
- Quantité d’adresses IP disponibles pour IP SSL
- Tailles de ressources de calcul utilisées par les serveurs frontaux ou les travaux (la taille minimale des serveurs frontaux est P2)

Vous ne pouvez pas modifier les éléments suivants :

- Lieu
- Abonnement
- Groupe de ressources
- Réseau virtuel utilisé
- Sous-réseau utilisé

Des détails supplémentaires sur la mise à l’échelle manuelle ainsi que la gestion et la surveillance des environnements App Service sont disponibles ici : [Configuration d’un environnement App Service][ASEConfig].

Pour plus d’informations sur la mise à l’échelle automatique, un guide est disponible dans la rubrique [Configuration de la mise à l’échelle automatique pour un environnement App Service][ASEAutoscale]

D'autres dépendances, telles que la base de données et le stockage, ne peuvent pas être personnalisées. Celles-ci sont gérées par Azure et sont fournies avec le système. Le stockage système prend en charge jusqu’à 500 Go pour l’ensemble de l’environnement App Service, et la base de données est ajustée par Azure en fonction de la mise à l’échelle du système.


## Prise en main
Tous les articles et procédures concernant les environnements App Service sont disponibles dans le [fichier Lisez-moi des environnements App Service](../app-service/app-service-app-service-environments-readme.md).

Pour prendre en main les environnements App Service, consultez [Présentation de l'environnement App Service][WhatisASE].

Pour plus d’informations sur la plateforme Azure App Service, consultez la rubrique [Azure App Service][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png
[4]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-externalvip.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/

<!---HONumber=AcomDC_0713_2016-->