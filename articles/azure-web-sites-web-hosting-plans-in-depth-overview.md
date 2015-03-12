<properties 
	pageTitle="Vue d'ensemble approfondie des plans d'hébergement web dans Sites Web Azure - Guide des fonctionnalités Azure" 
	description="Découvrez comment les plans d'hébergement web pour Sites Web Azure fonctionnent et en quoi ils améliorent votre expérience de gestion." 
	services="web-sites" 
	documentationCenter="" 
	authors="btardif" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="byvinyal, yochayk"/>
</br>
#Vue d'ensemble approfondie des plans d'hébergement web dans Sites web Azure#
</br>
Les plans d'hébergement web représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager sur vos sites web.  Ils prennent en charge les 4 options de tarification Sites Web Azure (Gratuit, Partagé, De base et Standard), chacune ayant ses propres possibilités.  Les sites des mêmes abonnement, groupe de ressources et emplacement géographique peuvent partager un plan d'hébergement web. Tous les sites qui partagent un plan d'hébergement web peuvent tirer profit de l'ensemble des possibilités définies par ce plan. Tous les sites web associés à un plan d'hébergement web utilisent les ressources définies par ce plan. Par exemple, si votre plan d'hébergement web est configuré pour utiliser deux " petites " machines virtuelles, tous les sites associés à ce plan utiliseront les deux machines virtuelles. Comme toujours avec les Sites web Azure, les machines virtuelles que vos sites exécutent sont hautement disponibles et entièrement gérées.
</br>
Dans cet article, nous allons explorer les principales caractéristiques telles que le niveau et l'échelle d'un plan d'hébergement web, et comment elles entrent en jeu lors de la gestion de vos sites web. 
</br>
##Sites web, plans d'hébergement web et groupes de ressources##
</br>
Un site web peut être associé à un seul plan d'hébergement web. Un plan d'hébergement web est associé à un groupe de ressources. Le concept de groupe de ressources est une nouveauté dans Azure, qui sert de frontière au cycle de vie de chaque ressource englobée. Les groupes de ressources permettent de gérer toutes les parties d'une application en même temps. 
</br>
Vous pouvez avoir plusieurs plans d'hébergement web dans un groupe de ressources. Chaque plan aura son propre ensemble de fonctionnalités utilisées par les sites associés.  L'image suivante illustre cette relation :
</br>
</br>
![Resource Groups and Web Hosting Plans](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png)
</br>
</br>
La possibilité d'avoir plusieurs plans d'hébergement web dans un même groupe de ressources vous permet d'allouer différents sites à différentes ressources, principalement les machines virtuelles exécutant vos sites web. Par exemple, cette fonctionnalité permet la séparation des ressources entre les sites de développement, de test et de production, où vous pouvez allouer un plan d'hébergement web avec son propre ensemble de ressources dédié à vos sites de production, et un second plan d'hébergement web à vos sites de développement et de test. 
</br>
Avoir plusieurs plans d'hébergement web dans un groupe de ressources unique permet de définir une application qui s'étend sur plusieurs régions. Par exemple, un site web hautement disponible qui s'exécute dans deux régions inclut deux plans d'hébergement web, un par région, ainsi qu'un site web associé à chaque plan d'hébergement web. Dans un tel cas, tous les sites sont associés à un seul groupe de ressources, qui définit une application.  Le fait de disposer d'une vue unique d'un groupe de ressources avec plusieurs plans d'hébergement web et plusieurs sites facilite la gestion, le contrôle et l'affichage de l'intégrité des sites web. Outre la gestion des ressources des sites web et des sites respectifs d'une application donnée, vous pouvez associer une ressource Azure connexe comme les bases de données SQL-Azure et les projets d'équipe. 
</br>
##Quand créer un groupe de ressources et quand créer un plan d'hébergement web ?##
</br>
Quand vous créez un site web, vous devez envisager de créer un groupe de ressources quand le site web qui va être créé représente une nouvelle application web. Dans ce cas, la bonne option consiste à créer un groupe de ressources, un plan d'hébergement web associé et des sites web.  Lorsque vous créez un site web avec la nouvelle version préliminaire du portail Azure, en utilisant la galerie ou le nouveau site web + l'option SQL, le portail crée par défaut un groupe de ressources et un plan d'hébergement web pour votre nouveau site. Cependant, si vous le souhaitez, vous pouvez remplacer ces valeurs par défaut.
</br>
</br>
![Creating a new Web Hosting Plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png)
</br>
</br>
Vous pouvez toujours ajouter un nouveau site web ou d'autres ressources à un groupe de ressources existant. Lorsque vous créez un site web dans le contexte d'un groupe de ressources existant, l'Assistant du nouveau site web prend par défaut la ressource et le plan d'hébergement web existants. Là encore, en cas de besoin, vous pouvez ignorer ces options par défaut. Quand vous ajoutez un site web à un groupe de ressources existant, vous pouvez choisir d'ajouter le site à un plan d'hébergement web existant (option par défaut du nouveau portail Azure en version préliminaire), ou vous pouvez créer un plan d'hébergement web auquel ajouter le site.
</br>
La création d'un plan d'hébergement vous permet d'allouer un nouveau jeu de ressources pour vos sites web, et de mieux contrôler l'allocation de ressources, car chaque plan d'hébergement web obtient son propre ensemble de machines virtuelles. Comme vous pouvez passer les sites web d'un plan d'hébergement web à un autre, sous réserve que les plans soient dans les mêmes régions, la décision de créer un nouveau plan d'hébergement est moins importante. Si un site web donné commence à consommer trop de ressources, ou si vous avez simplement besoin de séparer quelques sites web, vous pouvez créer un plan d'hébergement web et y déplacer vos sites web.
</br>
Si vous souhaitez créer un site web dans une autre région et que celle-ci n'a pas de plan d'hébergement web existant, vous devez en créer un dans cette région pour pouvoir y associer un site web. 
</br>
Une chose importante à garder à l'esprit est que vous ne pouvez pas déplacer des sites web ou des plans d'hébergement web entre des groupes de ressources. Vous ne pouvez pas non plus déplacer un site web d'un plan d'hébergement web vers un plan d'une autre région. 
</br>
##Groupes de ressources du portail Azure en version préliminaire##
</br>
Si vous avez déjà des sites web dans Sites web Azure, vous remarquerez qu'ils sont tous affichés dans le portail Azure en version préliminaire. Vous pouvez voir tous vos sites web dans une liste plate en cliquant sur le bouton **Parcourir** dans le volet de navigation gauche et en sélectionnant **Sites web** :
</br>
</br>
![See all your website as a flat list](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png)
</br>
</br>
Vous pouvez également voir tous les groupes de ressources que vous avez créés en cliquant sur le bouton **Parcourir** dans le volet de navigation gauche et en sélectionnant **Groupes de ressources** :
</br>
</br>
![See all the resource groups that have been created](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png)
</br>
</br>
Vous remarquerez également que vous disposez d'un groupe de ressources par défaut généré automatiquement dans chaque région dans laquelle vous avez déjà des sites web. Le nom du groupe de ressources généré automatiquement pour les sites web est *Default-Web-<NOM DE L'EMPLACEMENT>*, où le nom de l'emplacement représente une région Azure (par exemple *Default-Web-WestUS*). Dans chaque groupe de ressources, vous retrouvez tous vos sites pour la région du groupe. Chaque site que vous avez créé ou que vous allez créer dans la version préliminaire du portail Azure ou sa version complète sera disponible sur les deux portails. 
</br>
Étant donné que chaque site web doit être associé à un plan d'hébergement web, nous avons créé des plans d'hébergement web par défaut pour vos sites existants en fonction de la convention suivante, et ce dans chaque région :
</br>
* Tous vos sites web **gratuits** sont associés à un plan d'hébergement web **par défaut** avec l'option de tarification ayant la valeur **Gratuit**. 
</br>
* Tous vos sites web **partagés** sont associés à un plan d'hébergement web **par défaut** avec l'option de tarification ayant la valeur **Partagé**.
</br>
* Tous vos sites web **standard** sont associés à un plan d'hébergement web par défaut avec le niveau de tarification ayant la valeur **Standard**. 
</br>
Le nom de ce plan d'hébergement web est **DefaultServerFarm**. Il a été choisi pour prendre en charge une API héritée. Le nom **ServerFarm** peut être mal interprété, car il fait référence à un **Plan d'hébergement web**, mais il faut noter qu'il s'agit du nom d'un plan d'hébergement web, pas d'une entité à part entière. 
</br>
##Forum aux questions sur les plans d'hébergement web##
</br>
**Question** : Comment créer un plan d'hébergement web ?
</br>
**Réponse** : Un plan d'hébergement web est un conteneur. Vous ne pouvez donc pas créer de plan d'hébergement web vide. Toutefois, un plan d'hébergement web est explicitement créé pendant la création du site.
</br>
Pour ce faire, utilisez l'interface utilisateur du nouveau **portail Azure en version préliminaire**, cliquez sur **NOUVEAU**, puis sélectionnez **Site web** pour ouvrir le panneau de création de site web. Dans la première image ci-dessous, vous pouvez voir l'icône **NOUVEAU** en bas à gauche, et dans la seconde image, le panneau de création de **Site web**, le panneau **Plan d'hébergement web** et le panneau **Niveau de tarification** :
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png)
</br>
</br>
![Website, Web Hosting Plan and pricing tier blades](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png)
</br>
</br>
Pour cet exemple, nous avons choisi de créer un site web nommé **contosomarketing** et de le placer dans le nouveau plan d'hébergement web appelé **contoso**. L'option de tarification sélectionnée pour ce plan d'hébergement web est **Petit Standard**. Pour plus d'informations sur les niveaux de tarification des plans d'hébergement web, ainsi que sur les fonctionnalités, les options de tarification et d'extensibilité associées, consultez les [spécifications des plans d'hébergement web pour Sites web Azure](http://go.microsoft.com/?linkid=9845586). 
</br>
Il convient de noter qu'un plan d'hébergement web peut également être créé dans le portail Azure existant. Pour cela, utilisez l'Assistant **Création rapide** et sélectionnez **Créer un plan d'hébergement web** dans le menu déroulant **PLAN D'HÉBERGEMENT WEB** :
</br>
</br>
![Create new web hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png)
</br>
</br>
Pour cet exemple, nous créons un site appelé **northwind** et nous choisissons de créer un plan d'hébergement web. Cette opération aboutit au nouveau plan d'hébergement web **default0**, qui contient le site web **northwind**. Tous les plans d'hébergement web créés à l'aide de cette méthode suivent cette convention d'attribution de noms, et aucun plan ne peut être renommé. Les plans d'hébergement web créés à l'aide de cette méthode ont le niveau de tarification **Gratuit**.
</br>
**Question** : Comment affecter un site à un **plan d'hébergement web** ?
</br>
**Réponse** : Les sites sont affectés à un plan d'hébergement web au cours du processus de création du site. Pour ce faire, utilisez l'interface utilisateur du nouveau **portail Azure en version préliminaire**, cliquez sur **NOUVEAU** et sélectionnez **Site web** :
</br>
</br>
![Create a new website](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png)
</br>
</br>
Puis, dans le panneau de création de site web, sélectionnez le plan d'hébergement :
</br>
</br>
![Select a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png)
</br>
</br>
Un site peut également être créé dans un plan d'hébergement web spécifique à l'aide du portail Azure existant. Cette opération est effectuée avec l'Assistant **Création rapide**. Après avoir tapé l'URL du site web, utilisez le menu déroulant **PLAN D'HÉBERGEMENT WEB** pour sélectionner un plan auquel ajouter le site :
</br>
</br>
![Select a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png)
</br>
</br>
**Question** : Comment changer un site de plan d'hébergement web ?
</br>
**Réponse** : Vous pouvez changer un site de plan d'hébergement web à l'aide de la version préliminaire du portail Azure. Les sites web peuvent être déplacés entre des plans d'hébergement web d'une même région géographique qui appartiennent au même groupe de ressources.
</br>
Pour déplacer un site vers un autre plan, accédez au panneau du site web concerné.  Cliquez ensuite sur **Plan d'hébergement web** :
</br>
</br>
![Choose a new or existing web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png)
</br>
</br>
Cette action ouvre le panneau du plan d'hébergement web. À ce stade, vous pouvez choisir un plan d'hébergement web ou en créer un. Les plans d'un groupe de ressources ou emplacement géographique différent apparaissent en grisé et ne peuvent pas être sélectionnés.
</br>
Notez que chaque plan d'hébergement web a son propre niveau de tarification. Quand vous passez un site d'un plan d'hébergement web **Gratuit** à un plan d'hébergement web **Standard**, votre site web peut utiliser toutes les fonctionnalités et ressources du niveau Standard. 
</br>
</br>
**Question** : Comment mettre à l'échelle un plan d'hébergement web ?
</br>
**Réponse** : Deux méthodes permettent de mettre à l'échelle un plan d'hébergement web. L'une consiste à mettre à l'échelle le plan d'hébergement web et tous les sites associés. En modifiant le niveau de tarification d'un plan d'hébergement web, tous les sites associés sont soumis aux fonctionnalités et ressources définies par ce niveau de tarification. 
</br>
L'image ci-dessous présente les panneaux **Plan d'hébergement web** et **Niveau de tarification**. En cliquant sur la partie **Niveau de tarification** du panneau **Plan d'hébergement web**, vous développez le panneau **Niveau de tarification** où vous pouvez modifier le niveau de tarification du plan d'hébergement web :
</br>
</br>
![The Web Hosting Plan blade and the Pricing Tier](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png)
</br>
</br>
Une autre méthode consiste à mettre à l'échelle un plan en augmentant son nombre d'instances. L'image ci-dessous présente les panneaux **Plan d'hébergement web** et **Mettre à l'échelle**. Cliquez sur la zone Mettre à l'échelle du panneau **Plan d'hébergement web** pour le développer, ce qui vous permet de modifier le nombre d'instances du plan :
</br>
</br>
![Changing the instance count of a hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png)
</br>
</br>
Dans la mesure où le plan d'hébergement web dans l'image ci-dessus est configuré pour utiliser le niveau de tarification **Standard**, l'option **Mise à l'échelle automatique** est activée. 
</br>
Cette opération dans le portail Azure complet peut être effectuée sous l'onglet **Mettre à l'échelle**, comme indiqué ci-dessous :
</br>
</br>
![Changing the instance count of a hosting plan in the existing portal](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png)
</br>
</br>
**Question** : Comment supprimer un plan d'hébergement web ?
</br>
**Réponse** : Pour supprimer un plan d'hébergement web, vous devez d'abord supprimer tous les sites web associés. Une fois que tous les sites web d'un plan d'hébergement web ont été supprimés, ce dernier peut être supprimé du panneau Plan d'hébergement web :
</br>
</br>
![Deleting a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png)
</br>
</br>
Dans le portail Azure complet, la suppression du dernier site web d'un plan d'hébergement web supprime automatiquement le plan d'hébergement web associé.
</br>
**Question** : Comment surveiller un plan d'hébergement web ?
</br>
**Réponse** : Les plans d'hébergement web peuvent être surveillés à l'aide de la section Surveillance du panneau Plan d'hébergement web :
</br>
</br>
![Monitoring a web hosting plan](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png)
</br>
</br>
Les contrôles de surveillance peuvent être personnalisés en cliquant dessus avec le bouton droit et en sélectionnant **Modifier la requête** :
</br>
</br>
![Editing the monitoring controls](./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png)
</br>
</br>
Voici les mesures exposées :
</br>
* Pourcentage UC
</br>
* Pourcentage de mémoire
</br>
* Longueur de file d'attente de disque 
</br>
* Longueur de file d'attente HTTP. 
</br>
Ces mesures représentent l'usage moyen des instances appartenant à un plan d'hébergement web. Elles peuvent toutes servir à définir des alertes et des règles d'échelle automatique.
</br>
##Points essentiels et conclusions##
</br>
Les plans d'hébergement web représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager sur vos sites web.  Un plan d'hébergement web vous permet d'affecter des sites à un ensemble donné de ressources, les machines virtuelles, et optimise l'affectation des ressources Azure et l'utilisation des sites web.

<!--HONumber=35.2-->

<!--HONumber=46--> 
