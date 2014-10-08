<properties title="Azure Websites Web Hosting Plans In-Depth Overview" pageTitle="Azure Websites Web Hosting Plans In-Depth Overview - Windows Azure feature guide" description="Learn how Web Hosting Plans for Azure Websites work, and how they benefit your management experience." metaKeywords="Azure Web Sites, Azure Websites, WHP, Web Hosting Plan, Web Hosting Plans, Resource Groups" services="web-sites" solutions="web" documentationCenter="Infrastructure" authors="Byron Tardif and Yochay Kiryaty" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Byron Tardif and Yochay Kiryaty"></tags>
</br>

# Vue d'ensemble approfondie des plans d'hébergement Sites Web Azure

</br>
Les plans d'hébergement web représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager sur vos sites web. Ils prennent en charge les 4 options de tarification Sites Web Azure (Gratuit, Partagé, De base et Standard), chacune ayant ses propres possibilités. Les sites des mêmes abonnement, groupe de ressources et emplacement géographique peuvent partager un plan d'hébergement web. Tous les sites qui partagent un plan d'hébergement web peuvent tirer profit de l'ensemble des possibilités définies par ce plan. Tous les sites web associés à un plan d'hébergement web utilisent les ressources définies par ce plan. Par exemple, si votre plan d'hébergement web est configuré pour utiliser deux « petites » machines virtuelles, tous les sites associés à ce plan utiliseront les deux machines virtuelles. Comme toujours avec Sites Web Azure, les machines virtuelles utilisées par vos sites sont entièrement gérées et hautement disponibles.
</br>
Cet article explore les principales caractéristiques d'un plan d'hébergement web, comme les options et la mise à l'échelle, et leur mécanisme lors de la gestion de vos sites web.
</br>

## Sites web, plans d'hébergement web et groupes de ressources

</br>
Un site web peut être associé à un seul plan d'hébergement web. Un plan d'hébergement web est associé à un groupe de ressources. Le concept de groupe de ressources est une nouveauté dans Azure, qui sert de frontière au cycle de vie de chaque ressource englobée. Les groupes de ressources vous permettent de gérer en même temps toutes les parties d'une application.
</br>
Un groupe de ressources peut contenir plusieurs plans d'hébergement web, chacun ayant ses propres caractéristiques et possibilités utilisées par les sites associés. L'image suivante illustre cette relation :
</br>
</br>
![Resource Groups and Web Hosting Plans][]
</br>
</br>
Le fait qu'un seul groupe de ressources puisse avoir plusieurs plans d'hébergement web vous permet d'affecter différents sites à différentes ressources, essentiellement des machines virtuelles qui exécutent vos sites web. Par exemple, cela permet de séparer les ressources entre les sites de développement, de test et de production, lorsque vous souhaitez affecter un plan d'hébergement web avec ses propres ressources dédiées à vos sites de production et un second plan d'hébergement web à vos sites de développement et de test.
</br>
Le fait de disposer de plusieurs plans d'hébergement web dans un seul groupe de ressources permet également de définir une application qui couvre plusieurs régions. Par exemple, un site web hautement disponible qui s'exécute dans deux régions inclut deux sites web, un par région, et un site web associé à chaque plan d'hébergement web. Dans un tel cas, tous les sites sont associés à un seul groupe de ressources, qui définit une application. Le fait de disposer d'une vue unique d'un groupe de ressources avec plusieurs plans d'hébergement web et plusieurs sites facilite la gestion, le contrôle et l'affichage de l'intégrité des sites web. Outre la gestion des ressources des sites web et des sites respectifs d'une application donnée, vous pouvez associer une ressource Azure connexe comme les bases de données SQL-Azure et les Projets d'équipe.
</br>

## Quand créer un groupe de ressources et quand créer un plan d'hébergement web ?

</br>
Lorsque vous créez un site web, vous devez envisager de créer un groupe de ressources lorsque le site web qui va être créé représente une nouvelle application web. Dans ce cas, la bonne option consiste à créer un groupe de ressources, un plan d'hébergement web associé et des sites web. Lorsque vous créez un site web avec la nouvelle version préliminaire du portail Azure, en utilisant la galerie ou le nouveau site web + l'option SQL, le portail crée par défaut un groupe de ressources et un plan d'hébergement web pour votre nouveau site. En cas de besoin, vous pouvez ignorer ces valeurs par défaut.
</br>
</br>
![Creating a new Web Hosting Plan][]
</br>
</br>
Vous pouvez toujours ajouter un nouveau site web ou d'autres ressources à un groupe de ressources existant. Lorsque vous créez un site web dans le contexte d'un groupe de ressources existant, l'Assistant du nouveau site web prend par défaut la ressource et le plan d'hébergement web existants. Là encore, en cas de besoin, vous pouvez ignorer ces options par défaut. Lorsque vous ajoutez un nouveau site web à un groupe de ressources existant, vous pouvez ajouter le site à un plan d'hébergement web existant (il s'agit de l'option par défaut dans la nouvelle version préliminaire du portail Azure) ou vous pouvez créer un plan d'hébergement web auquel ajouter le site.
</br>
Créer un plan d'hébergement vous permet d'affecter de nouvelles ressources à vos sites web et de mieux maîtriser l'affectation des ressources, car chaque plan d'hébergement web dispose de ses propres machines virtuelles. Comme vous pouvez passer les sites web d'un plan d'hébergement web à un autre, sous réserve que les plans soient dans les mêmes régions, la décision de créer un nouveau plan d'hébergement est moins importante. Si un site web consomme soudainement trop de ressources ou si vous devez simplement séparer quelques sites web, vous pouvez créer un plan d'hébergement web pour y placer vos sites.
</br>
Si vous voulez créer un site web dans une autre région qui ne dispose pas de plan d'hébergement web, vous devez créer un plan dans cette région pour y associer un site.
</br>
N'oubliez pas que vous ne pouvez pas déplacer les plans d'hébergement web ni les sites web entre les groupes de ressources. Vous ne pouvez pas non plus déplacer un site web d'un plan d'hébergement web vers un plan d'une autre région.
</br>

## Groupes de ressources de la version préliminaire du portail Azure

</br>
Si vous avez déjà des sites web dans Sites Web Azure, vous remarquerez qu'ils sont tous affichés dans la version préliminaire du portail Azure. Vous pouvez voir tous vos sites web dans une liste plate en cliquant sur le bouton **Parcourir** dans le volet de navigation gauche et en sélectionnant **Sites web** :
</br>
</br>
![See all your website as a flat list][]
</br>
</br>
Vous pouvez également voir tous les groupes de ressources qui ont été créés pour vous en cliquant sur le bouton **Parcourir** dans le volet de navigation gauche et en sélectionnant **Groupes de ressources** :
</br>
</br>
![See all the resource groups that have been created][]
</br>
</br>
Vous pouvez également constater qu'il existe un groupe de ressources autogénéré par défaut dans chaque région dans laquelle vous avez déjà des sites web. Le nom du groupe de ressources autogénéré des sites web est *Default-Web-<location name>*, où le nom de l'emplacement représente une région Azure (par exemple *Default-Web-WestUS*). Dans chaque groupe de ressources, vous retrouvez tous vos sites pour la région du groupe. Chaque site que vous avez créé et que vous allez créer dans la version complète ou préliminaire du portail Azure est et sera disponible sur les deux portails.
</br>
Comme chaque site web doit être associé à un plan d'hébergement web, nous avons créé des plans d'hébergement web par défaut pour vos sites dans chaque région, conformément à la convention suivante :
</br>

-   Tous vos sites web **gratuits** sont associés à un plan d'hébergement web **par défaut** avec l'option de tarification paramétrée sur **Gratuit**.
    </br>
-   Tous vos sites web **partagés** sont associés à un plan d'hébergement web **par défaut** avec l'option de tarification paramétrée sur **Partagé**.
    </br>
-   Tous vos sites web **standard** sont associés à un plan d'hébergement web par défaut avec l'option de tarification paramétrée sur **Standard**.
    </br>
    Le nom de ce plan d'hébergement web est **DefaultServerFarm**. Il a été choisi pour prendre en charge une API héritée. Le nom **ServerFarm** peut être mal interprété, car il fait référence à un **Plan d'hébergement web**, mais il faut noter qu'il s'agit du nom d'un plan d'hébergement web, pas d'une entité à part entière.
    </br>

    ## Forum aux questions sur les plans d'hébergement web

    </br>
    **Question** : Comment créer un plan d'hébergement web ?
    </br>
    **Réponse** : Un plan d'hébergement web est un conteneur. Vous ne pouvez donc pas créer de plan d'hébergement web vide. Un plan d'hébergement web est explicitement créé pendant la création du site.
    </br>
    Dans l'interface de la nouvelle **version préliminaire du portail Azure**, cliquez sur **NOUVEAU** et sélectionnez **Site web** pour ouvrir le volet de création du site web. Dans la première image ci-dessous, en bas à gauche, est affichée l'icône **NOUVEAU**. Dans la deuxième image, vous pouvez voir le volet de création du **site web**, le volet **Plan d'hébergement web** et le volet **Option de tarification** :
    </br>
    </br>
    ![Create a new website][]
    </br>
    </br>
    ![Website, Web Hosting Plan and pricing tier blades][]
    </br>
    </br>
    Pour cet exemple, nous créons le site web **contosomarketing** que nous plaçons dans le nouveau plan d'hébergement web appelé **contoso**. L'option de tarification sélectionnée pour ce plan d'hébergement web est **Petit Standard**. Pour plus d'informations sur les options de tarification des plans d'hébergement web ainsi que sur les fonctionnalités et les options de tarification et de mise à l'échelle disponibles dans chacun, consultez la page des [spécifications des plans d'hébergement Sites Web Azure][].
    </br>
    Notez bien qu'un plan d'hébergement web peut également être créé dans le portail Azure existant. Pour ce faire, dans l'Assistant **Création rapide**, sélectionnez **Créer un plan d'hébergement web** dans le menu déroulant **PLAN D'HÉBERGEMENT WEB** :
    </br>
    </br>
    ![Create new web hosting plan in the existing portal][]
    </br>
    </br>
    Pour cet exemple, nous créons le site **northwind** et un plan d'hébergement web. Cette opération aboutit au nouveau plan d'hébergement web **default0**, qui contient le site web **northwind**. Tous les plans d'hébergement web créés à l'aide de cette méthode suivent cette convention d'attribution de noms, et aucun plan ne peut être renommé. Les plans d'hébergement web créés à l'aide de cette méthode ont l'option de tarification **Gratuit**.
    </br>
    **Question** : Comment affecter un site à un **plan d'hébergement web** ?
    </br>
    **Réponse** : Les sites sont affectés à un plan d'hébergement web au cours du processus de création du site. Pour ce faire, dans l'interface de la nouvelle **version préliminaire du portail Azure**, cliquez sur **NOUVEAU** et sélectionnez **Site web** :
    </br>
    </br>
    ![Create a new website][1]
    </br>
    </br>
    Dans le volet de création de site web, sélectionnez le plan d'hébergement :
    </br>
    </br>
    ![Select a hosting plan][]
    </br>
    </br>
    Un site peut être créé dans un plan d'hébergement web spécifique en utilisant le portail Azure existant. Cette opération est effectuée avec l'Assistant **Création rapide**. Après avoir tapé l'URL du site web, dans le menu déroulant **PLAN D'HÉBERGEMENT WEB**, sélectionnez un plan auquel ajouter le site :
    </br>
    </br>
    ![Select a hosting plan in the existing portal][]
    </br>
    </br>
    **Question** : Comment changer un site de plan d'hébergement web ?
    </br>
    **Réponse** : Vous pouvez changer un site de plan d'hébergement web à l'aide de la version préliminaire du portail Azure. Les sites web peuvent être déplacés entre des plans d'hébergement web de la même région géographique du même groupe de ressources.
    </br>
    Pour changer un site de plan, ouvrez le volet de site web du site à déplacer. Cliquez ensuite sur **Plan d'hébergement web** :
    </br>
    </br>
    ![Choose a new or existing web hosting plan][]
    </br>
    </br>
    Le volet du plan d'hébergement web s'ouvre. À ce stade, vous pouvez choisir un plan d'hébergement web ou en créer un. Les plans d'un autre emplacement géographique ou d'un autre groupe de ressources sont grisés et ne peuvent pas être sélectionnés.
    </br>
    Notez que chaque plan d'hébergement web a sa propre option de tarification. Lorsque vous passez un site d'un plan d'hébergement web avec l'option **Gratuit** à un plan d'hébergement web **Standard**, votre site web peut utiliser toutes les fonctionnalités et les ressources de l'option Standard.
    </br>
    </br>
    **Question** : Comment mettre à l'échelle un plan d'hébergement web ?
    </br>
    **Réponse** : Deux méthodes permettent de mettre à l'échelle un plan d'hébergement web. L'une consiste à mettre à l'échelle le plan d'hébergement web et tous les sites associés. En changeant l'option de tarification d'un plan d'hébergement web, tous les sites de ce plan sont soumis aux fonctionnalités et ressources définies par cette option.
    </br>
    L'image ci-dessous présente les volets **Plan d'hébergement web** et **Option de tarification**. Cliquer sur l'option **Option de tarification** dans le volet **Plan d'hébergement web** développe le volet **Option de tarification** dans lequel vous pouvez changer l'option de tarification du plan d'hébergement web :
    </br>
    </br>
    ![The Web Hosting Plan blade and the Pricing Tier][]
    </br>
    </br>
    La seconde méthode permettant de mettre à l'échelle un plan consiste à augmenter son nombre d'instances. L'image ci-dessous présente les volets **Plan d'hébergement web** et **Échelle**. Cliquer sur la zone Échelle du volet **Plan d'hébergement web** le développe et permet de changer le nombre d'instances du plan :
    </br>
    </br>
    ![Changing the instance count of a hosting plan][]
    </br>
    </br>
    Dans l'image ci-dessous, comme le plan d'hébergement web est configuré pour utiliser l'option de tarification **Standard**, l'option **Échelle automatique** est activée.
    </br>
    Cette opération peut être effectuée dans la version complète du portail Azure, sous l'onglet **Échelle**, comme indiqué ci-dessous :
    </br>
    </br>
    ![Changing the instance count of a hosting plan in the existing portal][]
    </br>
    </br>
    **Question** : Comment supprimer un plan d'hébergement web ?
    </br>
    **Réponse** : Pour supprimer un plan d'hébergement web, vous devez d'abord supprimer tous les sites web associés. Lorsque tous les sites web d'un plan d'hébergement web sont supprimés, il est possible de supprimer un plan d'hébergement web dans le volet du même nom :
    </br>
    </br>
    ![Deleting a web hosting plan][]
    </br>
    </br>
    Dans la version complète du portail Azure, la suppression du dernier site web d'un plan d'hébergement web supprime automatiquement le plan associé.
    </br>
    **Question** : Comment surveiller un plan d'hébergement web ?
    </br>
    **Réponse** : Les plans d'hébergement web peuvent être surveillés en utilisant les options de surveillance du volet du plan d'hébergement web :
    </br>
    </br>
    ![Monitoring a web hosting plan][]
    </br>
    </br>
    Les contrôles de surveillance peuvent être personnalisés en cliquant sur le contrôle avec le bouton droit et en sélectionnant **modifier la requête** :
    </br>
    </br>
    ![Editing the monitoring controls][]
    </br>
    </br>
    Les mesures exposées sont les suivantes :
    </br>
-   Pourcentage UC 
    </br>
-   Pourcentage de mémoire
    </br>
-   Longueur de file d'attente de disque
    </br>
-   Longueur de file d'attente HTTP.
    </br>
    Ces mesures représentent l'usage moyen des instances appartenant à un plan d'hébergement web. Elles peuvent toutes servir à définir des alertes et des règles d'échelle automatique.
    </br>

    ## Points essentiels et conclusions

    </br>
    Les plans d'hébergement web représentent un ensemble de fonctionnalités et de capacités que vous pouvez partager sur vos sites web. Un plan d'hébergement web vous permet d'affecter des sites à un ensemble donné de ressources, les machines virtuelles, et optimise l'affectation des ressources Azure et l'utilisation des sites web.

  [Resource Groups and Web Hosting Plans]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview01.png
  [Creating a new Web Hosting Plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview02.png
  [See all your website as a flat list]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview03.png
  [See all the resource groups that have been created]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview04.png
  [Create a new website]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview05.png
  [Website, Web Hosting Plan and pricing tier blades]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview06.png
  [spécifications des plans d'hébergement Sites Web Azure]: http://go.microsoft.com/?linkid=9845586
  [Create new web hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview07.png
  [1]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview08.png
  [Select a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview09.png
  [Select a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview10.png
  [Choose a new or existing web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview22.png
  [The Web Hosting Plan blade and the Pricing Tier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview16.png
  [Changing the instance count of a hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview17.png
  [Changing the instance count of a hosting plan in the existing portal]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview18.png
  [Deleting a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview19.png
  [Monitoring a web hosting plan]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview20.png
  [Editing the monitoring controls]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/azure-web-sites-web-hosting-plans-in-depth-overview21.png
