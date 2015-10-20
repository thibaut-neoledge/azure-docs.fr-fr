<properties
   pageTitle="Gérer des profils Azure Traffic Manager | Microsoft Azure"
   description="Cet article vous aide à créer, désactiver, activer, supprimer et afficher l’historique d’un profil Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/19/2015"
   ms.author="joaoma" />

# Gestion d’un profil Azure Traffic Manager

Un profil Traffic Manager vous permet de spécifier les services cloud ou les points de terminaison de site web qui seront surveillés par Traffic Manager et la méthode de routage du trafic à utiliser pour distribuer les connexions à ces points de terminaison.

## Création d’un profil Traffic Manager avec l’option Création rapide

Vous pouvez créer un profil Traffic Manager rapidement à l’aide de l’option Création rapide du portail Azure. Cette option vous permet de créer des profils dotés de paramètres de configuration de base. Toutefois, vous ne pouvez pas utiliser la Création rapide pour définir des paramètres tels que l’ensemble de points de terminaison (services cloud et sites web), l’ordre de basculement pour la méthode de routage du trafic de basculement ou la surveillance. Après avoir créé votre profil, vous pouvez configurer ces paramètres dans le portail Azure. Traffic Manager prend en charge jusqu’à 200 points de terminaison par profil. Toutefois, la plupart des scénarios d’utilisation requièrent uniquement un petit nombre de points de terminaison.

### Création d’un profil Traffic Manager

1. **Déployez vos services cloud et sites web vers votre environnement de production.** Pour en savoir plus sur les services cloud, consultez [Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=314074). Pour en savoir plus sur les services cloud, consultez la page [Meilleures pratiques](https://msdn.microsoft.com/library/azure/5229dd1c-5a91-4869-8522-bed8597d9cf5#bkmk_TrafficManagerBestPracticesProfile). Pour en savoir plus sur les sites web, consultez [Sites web](http://go.microsoft.com/fwlink/p/?LinkId=393327).

2. **Connectez-vous au portail Azure.** Pour créer un profil Traffic Manager, cliquez sur **Nouveau** dans l’angle inférieur gauche du portail, puis cliquez sur **Network Services > Traffic Manager**, puis **Création rapide** pour commencer à configurer votre profil.
3. **Configurez le préfixe DNS.** Donnez un nom de préfixe DNS unique à votre profil Traffic Manager. Vous pouvez spécifier uniquement le préfixe d’un nom de domaine Traffic Manager.
4. **Sélectionnez l’abonnement.** Sélectionnez l’abonnement Azure approprié. Chaque profil est associé à un seul abonnement. Si vous ne disposez que d’un abonnement, cette option n’apparaît pas.
5. **Sélection de la méthode de routage du trafic** Sélectionnez la méthode de routage du trafic dans la **stratégie de routage du trafic**. Pour plus d’informations sur les différentes méthodes de routage du trafic, consultez [À propos des méthodes de routage du trafic de Traffic Manager](traffic-manager-load-balancing-methods.md).
6. **Cliquez sur « Créer » pour créer votre profil**. Lorsque la configuration du profil est terminée, recherchez votre profil dans le volet Traffic Manager du portail Azure.
7. **Configurez des points de terminaison, la surveillance et des paramètres supplémentaires dans le portail Azure.** Comme vous ne pouvez configurer que des paramètres de base à l’aide de l’option Création rapide, il est nécessaire de configurer d’autres paramètres, par exemple la liste des points de terminaison et l’ordre de basculement des points de terminaison afin de terminer la configuration de votre choix. 


## Désactiver, activer ou supprimer un profil

Vous pouvez désactiver un profil Traffic Manager existant afin qu’il ne renvoie pas les demandes utilisateur vers ses points de terminaison configurés. Lorsque vous désactivez un profil Traffic Manager, le profil et les informations qu’il contient demeurent intacts et peuvent être modifiés via l’interface de Traffic Manager. Vous pouvez réactiver le profil facilement via le portail Azure. Dans ce cas, les références seront rétablies. Lorsque vous créez un profil Traffic Manager dans le portail Azure, il est automatiquement activé. Si un profil ne vous semble plus nécessaire, vous pouvez le supprimer.

### Désactivation d’un profil

1. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement approprié et un pointeur adéquat vers un autre nom ou vers l’adresse IP d’un emplacement spécifique sur Internet. En d’autres termes, modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin qu’il n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
2. Le trafic ne sera plus dirigé vers les points de terminaison via les paramètres du profil Traffic Manager.
3. Sélectionnez le profil que vous souhaitez désactiver. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
4. Après avoir sélectionné le profil, cliquez sur **Désactiver** en bas de la page.

### Activation d’un profil

1. Sélectionnez le profil que vous souhaitez activer. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
2. Après avoir sélectionné le profil, cliquez sur **Activer** en bas de la page.
3. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement CNAME, qui mappe votre nom de domaine d’entreprise au nom de domaine de votre profil Traffic Manager. Pour plus d’informations, consultez la page [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](traffic-manager-point-internet-domain.md).
4. Le trafic est de nouveau dirigé vers les points de terminaison.

### Suppression d’un profil

1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
2. Sélectionnez le profil que vous souhaitez supprimer. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
4. Après avoir sélectionné le profil, cliquez sur **Supprimer** en bas de la page.

## Afficher l'historique des modifications d'un profil Traffic Manager

Vous pouvez afficher l’historique des modifications de votre profil Traffic Manager dans le portail Azure, dans les services de gestion.

### Pour afficher votre historique des modifications Traffic Manager

1. Dans le volet gauche du portail Azure, cliquez sur **Services de gestion**.
2. Dans la page Services de gestion, cliquez sur **Journaux des opérations**.
3. Dans la page Journaux des opérations, vous pouvez filtrer pour afficher l’historique des modifications de votre profil Traffic Manager. Après avoir sélectionné les options de filtrage, cliquez sur la coche pour afficher les résultats.
   - Pour afficher les modifications de tous vos profils, sélectionnez votre abonnement et l’intervalle de temps, puis **Traffic Manager** dans le menu contextuel **Type**.
   - Pour filtrer par nom de profil, tapez le nom du profil dans le champ **Nom du service** ou sélectionnez-le dans le menu contextuel.
   - Pour afficher les détails de chaque modification, sélectionnez la ligne contenant la modification à afficher, puis cliquez sur **Détails** en bas de la page. Dans la fenêtre **Détails de l’opération**, vous pouvez afficher la représentation XML de l’objet API qui a été créé ou mis à jour dans le cadre de l’opération et copier le code XML dans le Presse-papiers.


## Ressources supplémentaires

[Qu’est-ce que Traffic Manager ?](traffic-manager-overview.md)

[Gérer des points de terminaison dans Traffic Manager](traffic-manager-endpoints.md)

[À propos de la surveillance avec Traffic Manager](traffic-manager-monitoring.md)

[Traffic Manager : désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Traffic Manager : désactiver ou activer un point de terminaison](disable-or-enable-an-endpoint.md)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

[Services cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)

<!---HONumber=Oct15_HO3-->