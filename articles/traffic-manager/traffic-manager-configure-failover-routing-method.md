<properties 
   pageTitle="Configurer une méthode de routage du trafic de Traffic Manager | Microsoft Azure"
   description="Cet article vous aide à configurer une méthode de routage du trafic par basculement dans Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2016"
   ms.author="sewhee" />

# Configurer la méthode de routage par basculement

Bien souvent, une organisation souhaite assurer une certaine fiabilité pour ses services. Pour ce faire, elle fournit des services de sauvegarde au cas où le service principal serait hors fonction. Dans le cadre du basculement des services, il est courant d'utiliser un ensemble de services identiques et de diriger le trafic vers un service principal, tout en gérant une liste configurée répertoriant une ou plusieurs services de sauvegarde. Vous pouvez configurer ce type de sauvegarde avec les services cloud Azure et les sites web en suivant les procédures ci-dessous.

Notez qu'Azure Websites fournit déjà des fonctionnalités de routage du trafic par basculement pour les sites Web d’un centre de données (également appelé région), indépendamment du mode de site Web. Traffic Manager vous permet de spécifier une méthode de routage du trafic par basculement et tourniquet (round robin) pour des sites Web de différents centres de données.

## Pour configurer une méthode de routage de trafic par basculement :

1. Dans le volet gauche du portail Azure Classic, cliquez sur l’icône **Traffic Manager** pour ouvrir le volet Traffic Manager. Si vous n’avez pas encore créé votre profil Traffic Manager, consultez [Gérer les profils Traffic Manager](traffic-manager-manage-profiles.md) pour connaître les étapes de création d’un profil Traffic Manager de base.
2. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
3. Dans la page de votre profil, cliquez sur **Points de terminaison** en haut de la page et vérifiez que les services cloud et les sites web (points de terminaison) que vous souhaitez inclure dans votre configuration sont présents. Pour savoir comment ajouter ou supprimer des points de terminaison, consultez [Gérer les points de terminaison dans Traffic Manager](traffic-manager-endpoints.md).
4. Dans la page de votre profil, cliquez sur **Configurer** en haut pour ouvrir la page de configuration.
5. Pour **Paramètres de méthode de routage de trafic**, vérifiez que la méthode de routage du trafic est **Basculement**. Si ce n’est pas le cas, cliquez sur **Basculement** dans la liste déroulante.
6. Pour **Liste prioritaire de basculement**, ajustez l’ordre de basculement de vos points de terminaison. Lorsque vous configurez la méthode de routage du trafic par **Basculement**, l'ordre des points de terminaison sélectionnés est important. Le point de terminaison principal doit se trouver en haut de la liste. Utilisez les flèches vers le haut et vers le bas pour modifier l'ordre si besoin. Pour plus d’informations sur la définition de la priorité de basculement à l’aide de Windows PowerShell, consultez [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Vérifiez que les **Paramètres de surveillance** sont correctement configurés. La surveillance permet de s'assurer qu'aucun trafic n'est dirigé vers les points de terminaison désactivés. Pour surveiller les points de terminaison, vous devez indiquer un chemin et un nom de fichier. Notez qu'une barre oblique « / » est une entrée valide pour le chemin d'accès relatif et qu'elle implique que le fichier se trouve dans le répertoire racine (par défaut). Pour plus d’informations sur la surveillance, consultez [Surveillance avec Traffic Manager](traffic-manager-monitoring.md).
8. Une fois que vous avez terminé de modifier la configuration, cliquez sur **Enregistrer** en bas de la page.
9. Testez les modifications dans votre configuration. Pour plus d’informations, consultez [Test des paramètres de Traffic Manager](traffic-manager-testing-settings.md).
10. Une fois le profil Traffic Manager configuré et opérationnel, modifiez l'enregistrement DNS sur le serveur DNS faisant autorité, afin de faire pointer votre nom de domaine d'entreprise vers le nom de domaine Traffic Manager. Pour plus d’informations sur la marche à suivre, consultez [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).

## Étapes suivantes

[Rediriger un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md)

[Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md)

[Configurer la méthode de routage du trafic en tourniquet (round robin)](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage basé sur les performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager - Désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

 

<!---HONumber=AcomDC_0824_2016-->