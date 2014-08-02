<properties linkid="web-sites-traffic-manager" urlDisplayName="Controlling Windows Azure Web Sites Traffic with Azure Traffic Manager" pageTitle="Controlling Azure Web Sites Traffic with Azure Traffic Manager" metaKeywords="Azure Web Sites, Traffic Manager, request routing, round robin, failover, performance" description="This article provides summary information for  Azure Traffic Manager as it relates to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Controlling Azure Web Sites Traffic with Azure Traffic Manager" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Contrôle du trafic du service Sites Web Azure avec Azure Traffic Manager
========================================================================

> [WACOM.NOTE] Cet article présente des informations succinctes sur Microsoft Azure Traffic Manager, qui est associé au service Sites Web Azure. Vous trouverez d'autres informations sur Azure Traffic Manager en suivant les liens fournis à la fin de cet article.

Introduction
------------

Vous pouvez utiliser Azure Traffic Manager pour contrôler la distribution des demandes des clients web sur le service Sites Web Azure. Lorsque des points de terminaison de sites web Azure sont ajoutés à un profil Azure Traffic Manager, ce dernier conserve une trace du statut de vos sites web (en cours d'exécution, arrêté ou supprimé) pour pouvoir décider lequel de ces points de terminaison doit recevoir le trafic.

Méthodes d'équilibrage de charge
--------------------------------

Azure Traffic Manager utilise trois méthodes d'équilibrage de charge. Elles sont décrites dans la liste suivante, car elles se rapportent au service Sites Web Azure.

-   **Basculement** : si vous avez des clones de sites web dans différentes régions, vous pouvez utiliser cette méthode pour configurer un site web pour assurer la totalité du trafic du client web et en configurer un autre d'une autre région pour assurer ce trafic en cas de défaillance du premier site web.

-   **Tourniquet** : si vous avez des clones de sites web dans différentes régions, vous pouvez utiliser cette méthode pour distribuer le trafic à parts égales sur les sites web des différentes régions.

-   **Performance** : la méthode de Performance distribue le trafic sur la base de l'aller-retour le plus court vers les clients. Cette méthode peut être utilisée pour les sites web d'une même région ou de régions différentes.

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, consultez la rubrique [À propos des méthodes d'équilibrage de charge dans Traffic Manager](http://msdn.microsoft.com/en-us/library/windowsazure/dn339010.aspx).

Sites Web Azure et profils Traffic Manager
------------------------------------------

Pour configurer le contrôle du trafic des sites web, vous pouvez créer un profil dans Azure Traffic Manager, qui utilise l'une des trois méthodes d'équilibrage de charge décrites précédemment, puis ajouter les points de terminaison (ici, des sites web) dont vous souhaitez contrôler le trafic sur le profil. Le statut de votre site web (en cours d'exécution, arrêté ou supprimé) est régulièrement communiqué au profil ce qui permet à Azure Traffic Manager de diriger le trafic.

Lorsque vous utilisez Azure Traffic Manager avec Azure, tenez compte des points suivants :

-   Pour les déploiements de sites web uniquement dans la même région, Sites Web Azure fournit déjà les fonctionnalités de basculement et de tourniquet indépendamment du mode du site web.

-   Pour les déploiements dans la même région qui utilisent Sites Web Azure conjointement avec un autre service cloud Azure, vous pouvez combiner les types de points de terminaison pour autoriser les scénarios hybrides.

-   Vous pouvez spécifier un seul point de terminaison de site web par région dans un profil. Lorsque vous sélectionnez un site web comme point de terminaison pour une région, les autres sites web de cette région ne peuvent plus être sélectionnés pour ce profil.

-   Les points de terminaison de site web que vous spécifiez dans un profil Azure Traffic Manager figurent dans la section **Domain Names** sur la page de configuration des sites web du profil, mais ne peuvent pas être configurés ici.

-   Lorsque vous avez ajouté un site web à un profil, l'**Site URL** sur le tableau de bord de la page du portail du site web affiche l'URL de domaine personnalisé du site web, le cas échéant. Sinon, elle affiche l'URL du profil Traffic Manager (par exemple, `contoso.trafficmgr.com`). Le nom de domaine direct du site web et l'URL Traffic Manager sont tous deux affichés sur la page de configuration du site web dans la section **Domain Names**.

-   Vos noms de domaine personnalisé fonctionnent comme prévu, mais, en plus de les ajouter à vos sites web, vous devez configurer votre carte DNS pour qu'elle pointe sur l'URL Traffic Manager. Pour plus d'informations sur la configuration d'un domaine personnalisé pour un site web Azure, consultez la page [Configuration d'un nom de domaine personnalisé pour un site web Azure](https://www.windowsazure.com/en-us/documentation/articles/web-sites-custom-domain-name/).

-   Vous pouvez uniquement ajouter des sites web qui sont en mode Standard dans un profil Azure Traffic Manager.

Étapes suivantes
----------------

Pour une vue d'ensemble conceptuelle et technique d'Azure Traffic Manager, consultez la rubrique [Vue d'ensemble de Traffic Manager](http://msdn.microsoft.com/en-us/library/windowsazure/hh744833.aspx).

Pour plus d'informations sur la configuration d'Azure Traffic Manager, y compris pour l'utilisation de Sites Web Azure, consultez la rubrique [Tâches de configuration Traffic Manager](http://msdn.microsoft.com/en-us/library/windowsazure/hh744830.aspx).

Pour plus d'informations sur l'équilibrage de charge dans Azure Traffic Manager, consultez la rubrique [À propos des méthodes d'équilibrage de charge dans Traffic Manager](http://msdn.microsoft.com/en-us/library/windowsazure/dn339010.aspx).

