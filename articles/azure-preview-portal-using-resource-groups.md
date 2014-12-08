<properties urlDisplayName="" pageTitle="Utilisation des groupes de ressources pour gérer vos ressources Azure" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Using Resource groups to manage your Azure resources" authors="Nafisa Bhojawala"  solutions="" writer="" manager="terrylan" editor=""  />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Nafisa Bhojawala" />


# Utilisation des groupes de ressources pour gérer vos ressources Azure

### Introduction

Avant, pour gérer une ressource (une entité gérée par l'utilisateur, comme un serveur de base de données, une base de données ou un site web) dans Microsoft Azure, vous deviez exécuter les opérations sur une ressource à la fois. Avec une application complexe constituée de plusieurs ressources, la gestion devenait un casse-tête. Dans la version préliminaire du portail Azure, vous pouvez créer des groupes de ressources pour gérer toutes les ressources d'une application en même temps. Le concept de groupe de ressources est une nouveauté dans Azure, qui sert de limite de cycle de vie pour chaque ressource englobée. 
<br><br />

Les groupes de ressources permettent de gérer toutes les ressources d'une application en même temps. Ils sont activés par la nouvelle fonctionnalité de gestion, le Gestionnaire de ressources Azure. Le Gestionnaire de ressources Azure permet de regrouper plusieurs ressources dans un groupe logique qui sert de limite de cycle de vie pour chaque ressource englobée. En général, un groupe contient des ressources associées à une application spécifique. Par exemple, un groupe peut contenir une ressource de site web qui héberge votre site web public, une base de données SQL qui stocke les données relationnelles utilisées par le site et un compte de stockage qui stocke les ressources non relationnelles. 

Voici une présentation succincte de la façon dont vous pouvez utiliser les groupes de ressources dans la version préliminaire du portail Azure. 
<br><br />

### Création de groupes de ressources

Lorsqu'une ressource est créée dans la version préliminaire du portail, elle est toujours intégrée à un groupe de ressources. Vous avez le choix entre créer un groupe de ressources et utiliser un groupe qui existe déjà dans le flux de création. <br><br />

![](http://i.imgur.com/USKkQdW.png)

<br><br />
Lorsque vous créez une application qui comporte quelques ressources fonctionnant ensemble (par exemple un site web + une base de données), elle est toujours créée dans son propre groupe de ressources, ce qui vous permet de gérer le cycle de vie de l'ensemble des ressources associées en utilisant le groupe. Vous pouvez ajouter des ressources au groupe ou en supprimer au fil de l'évolution de votre application. 

![](http://i.imgur.com/Me0jbio.png)


<br><br />

### Consultation des groupes de ressources

Vous pouvez consulter tous les groupes de ressources en cliquant sur la barre sur le côté gauche de l'écran. Un groupe de ressources comporte un volet qui affiche toutes les informations sur ce groupe de ressources particulier. Il permet également d'obtenir une vue unifiée des informations de facturation et de surveillance pour toutes les ressources du groupe.

Le résumé affiche une carte schématique de l'ensemble des ressources du groupe et de celles d'autres groupes qui lui sont liés. La carte des ressources indique également l'état de chaque ressource. 
![](http://i.imgur.com/PhJeLZQ.png)

<br><br />

La carte des ressources peut être personnalisée et agrandie pour afficher toutes les ressources d'un groupe et celles d'autres groupes liés. Elle peut être épinglée sur le tableau d'accueil, qui la copiera.

![](http://i.imgur.com/5Wqv2XR.png)

<br><br />

  Un clic sur la carte des ressources permet de lancer la vue liste de toutes les ressources de la carte. Cette vue répertorie toutes les ressources d'un groupe ou les ressources associées. Un clic sur ces ressources permet de lancer leur volet. 

![](http://i.imgur.com/COPjNng.png)




<br><br />

### Ajout de ressources à des groupes de ressources

Vous pouvez ajouter des ressources à un groupe de ressources au moyen de la commande Ajouter sur le volet du groupe de ressources. Pour ajouter des ressources au groupe de ressources, suivez la procédure du flux.

![](http://i.imgur.com/G79kayH.png)

Remarque : il est déconseillé de placer un projet d'équipe dans le même groupe de ressources que d'autres ressources Azure. Si vous créez un projet d'équipe dans un nouveau compte et un nouveau groupe, puis créez un site web, le groupe de site sera automatiquement le dernier groupe utilisé (VSO), ce qui mélangera ressources du runtime et ressources de développement dans le même groupe. 



<br><br />

### Suppression de groupes de ressources

Comme les groupes de ressources permettent de gérer le cycle de vie de l'ensemble des ressources englobées, la suppression d'un groupe de ressources entraîne celle de toutes les ressources qu'il contient. Vous pouvez également supprimer des ressources individuelles d'un groupe de ressources. Soyez prudent lorsque vous supprimez un groupe de ressources, car d'autres ressources peuvent y être liées. Vous pouvez voir toutes les ressources liées dans la carte des ressources et prendre les mesures nécessaires pour éviter des conséquences fâcheuses lorsque vous supprimez des groupes de ressources. 

![](http://i.imgur.com/ZTXoISb.png)
