---
title: "Profils Traffic Manager imbriqués | Microsoft Docs"
description: "Cet article explique la fonctionnalité des profils imbriqués d’Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 3e48a28aa1ecda6792e79646a33875c8f01a878f
ms.openlocfilehash: fdf22a3f8d0ba6f1838af4f5e6924c8c0a18ef64

---

# <a name="nested-traffic-manager-profiles"></a>Profils Traffic Manager imbriqués

Traffic Manager comprend une série de méthodes de routage du trafic qui vous permet de contrôler la manière dont Traffic Manager choisit le point de terminaison qui doit recevoir le trafic de chaque utilisateur final. Pour plus d’informations, consultez la rubrique relative aux [méthodes de routage du trafic dans Traffic Manager](traffic-manager-routing-methods.md).

Chaque profil Traffic Manager spécifie une seule méthode de routage du trafic. Cependant, certains scénarios exigent une méthode de routage du trafic plus sophistiquée que celle proposée par un profil Traffic Manager unique. Vous pouvez imbriquer des profils Traffic Manager pour combiner les avantages de plusieurs méthodes de routage du trafic. Les profils imbriqués permettent de modifier le comportement par défaut de Traffic Manager pour prendre en charge des déploiements d’applications plus importants et plus complexes.

Les exemples suivants illustrent l’utilisation de profils Traffic Manager imbriqués dans divers scénarios.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Exemple 1: combinaison de routage du trafic « performant (Performance) » et « pondéré (Weighted) »

Supposons que vous avez déployé une application dans les régions Azure suivantes : États-Unis de l’Ouest, Europe de l’Ouest et Asie de l’Est. Vous utilisez la méthode de routage du trafic « Performance » de Traffic Manager pour acheminer le trafic vers la région la plus proche de l’utilisateur.

![Profil Traffic Manager unique][1]

Maintenant, supposons que vous souhaitez tester une mise à jour de votre service avant de déployer celui-ci plus largement. Vous souhaitez utiliser la méthode de routage du trafic « Pondéré » pour acheminer un petit pourcentage du trafic vers votre déploiement de tests. Vous configurez le déploiement de tests en même temps que le déploiement de production existant en Europe de l’Ouest.

Vous ne pouvez pas combiner les routages de trafic « Pondéré » et « Performance » dans un seul profil. Pour prendre en charge ce scénario, vous créez un profil Traffic Manager en utilisant les deux points de terminaison d’Europe de l’Ouest et la méthode de routage du trafic « Pondéré ». Ensuite, vous ajoutez ce profil « enfant » en tant que point de terminaison au profil « parent ». Le profil parent utilise encore la méthode de routage du trafic « Performances », et contient les autres déploiements globaux en tant que points de terminaison.

Le schéma suivant illustre cet exemple :

![Profils Traffic Manager imbriqués][2]

Dans cette configuration, le trafic dirigé via le profil parent distribue le trafic entre les régions normalement. En Europe de l’Ouest, le profil imbriqué distribue le trafic vers les points de terminaison de production et de test en fonction des pondérations assignées.

Lorsque le profil parent utilise la méthode de routage du trafic « Performance », un emplacement doit être assigné à chaque point de terminaison. L’emplacement est assigné lorsque vous configurez le point de terminaison. Choisissez la région Azure la plus proche de votre déploiement. Les régions Azure sont les valeurs d’emplacement prises en charge par la Table de latence Internet. Pour plus d’informations, voir [Méthode de routage du trafic « Performance » d’Azure Traffic Manager](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Exemple 2 : analyse de points de terminaison dans des profils imbriqués

Traffic Manager surveille activement l'intégrité de chaque point de terminaison de service. Si un point de terminaison n’est pas intègre, Traffic Manager dirige les utilisateurs vers d’autres points de terminaison pour préserver la disponibilité de votre service. Ce comportement de surveillance et de basculement des points de terminaison s’applique à toutes les méthodes de routage du trafic. Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md). La surveillance des points de terminaison fonctionne différemment pour les profils imbriqués. Avec des profils imbriqués, le profil parent n’effectue pas de contrôles d’intégrité directement sur le profil enfant. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant. Ces informations d’intégrité sont propagées vers le haut de la hiérarchie de profils imbriqués. Le profil parent utilise cette intégrité agrégée pour déterminer s’il faut diriger le trafic vers le profil enfant. Pour plus d’informations sur la surveillance de l’intégrité des profils imbriqués, voir la section [FAQ](#faq) de cet article.

En revenant à l’exemple précédent, supposons que le déploiement de production en Europe de l’Ouest est défaillant. Par défaut, le profil « enfant » dirige tout le trafic vers le déploiement de tests. Si le déploiement de tests échoue également, le profil parent détermine que le profil enfant ne doit pas recevoir de trafic, car aucun des points de terminaison enfants n’est intègre. Ensuite, le profil parent distribue le trafic aux autres régions.

![Basculement de profil imbriqué (comportement par défaut)][3]

Il se peut que vous soyez satisfait de cette organisation. Il se peut également que vous vous inquiétiez du fait que tout le trafic pour l’Europe de l’Ouest aille désormais vers le déploiement de tests, au lieu du trafic d’un sous-ensemble limité. Quelle que soit l’intégrité du déploiement du test, vous souhaitez basculer vers les autres régions en cas d’échec du déploiement de production en Europe de l’Ouest. Pour activer ce basculement, vous pouvez spécifier le paramètre « MinChildEndpoints » lorsque vous configurez le profil enfant en tant que point de terminaison dans le profil parent. Le paramètre détermine le nombre minimal de points de terminaison disponibles dans le profil de l’enfant. La valeur par défaut est « 1 ». Pour ce scénario, vous définissez la valeur de MinChildEndpoints sur 2. Sous ce seuil, le profil parent considère que le profil enfant entier est indisponible et dirige le trafic vers les autres points de terminaison.

Le schéma suivant illustre cette configuration :

![Basculement de profil imbriqué avec « MinChildEndpoints » = 2][4]

> [!NOTE]
> La méthode de routage du trafic « Priorité » distribue tout le trafic vers un seul point de terminaison. Il est par conséquent peu utile de définir un paramètre MinChildEndpoints autre que « 1 » pour un profil enfant.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Exemple 3 : basculement hiérarchisé des régions dans le routage du trafic de type « Performance »

Le comportement par défaut pour la méthode de routage du trafic « Performance » est conçu pour éviter une surcharge du point de terminaison le plus proche qui entraînerait une série d’échecs en cascade. En cas de défaillance d’un point de terminaison, tout le trafic qui aurait été dirigé vers celui-ci est réparti uniformément entre les autres points de terminaison dans toutes les régions.

![Routage du trafic « Performance » avec basculement par défaut][5]

Toutefois, supposons que vous préfériez basculer le trafic vers l’Europe de l’Ouest plutôt que vers les États-Unis de l’Ouest, et diriger le trafic vers d’autres régions uniquement lorsque les deux points de terminaison sont indisponibles. Vous pouvez créer cette solution en utilisant un profil enfant avec la méthode de routage du trafic « Priorité ».

![Routage du trafic « Performance » avec basculement préférentiel][6]

Étant donné que le point de terminaison d’Europe de l’Ouest a une priorité plus élevée que le point de terminaison des États-Unis de l’Ouest, tout le trafic est envoyé au point de terminaison d’Europe de l’Ouest lorsque les deux points de terminaison sont en ligne. En cas de défaillance du point de terminaison Europe de l’Ouest, le trafic est dirigé vers les États-Unis de l'Ouest. Avec le profil imbriqué, le trafic est dirigé vers l’Asie de l’Est uniquement en cas de défaillances des points de terminaison d’Europe de l’Ouest et des États-Unis de l’Ouest.

Vous pouvez répéter ce modèle pour toutes les régions. Remplacez les trois points de terminaison dans le profil parent par trois profils enfants, chacun offrant une séquence de basculement hiérarchisée.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Exemple 4 : contrôle du routage du trafic de type « Performance » entre plusieurs points de terminaison dans la même région

Supposons que la méthode de routage du trafic « Performance » est utilisée dans un profil qui a plusieurs points de terminaison dans une région spécifique. Par défaut, le trafic dirigé vers cette région est réparti uniformément entre tous les points de terminaison disponibles dans cette région.

![Distribution du trafic au sein d’une région dans le cadre d’un routage du trafic « Performance » (comportement par défaut)][7]

Au que plusieurs points de terminaison soient ajoutés en Europe de l’Ouest, ces points de terminaison sont regroupés dans un profil enfant distinct. Le profil enfant est ajouté au parent en tant que seul point de terminaison en Europe de l’Ouest. Les paramètres du profil enfant peuvent contrôler la distribution du trafic vers l’Europe de l’Ouest en permettant un routage du trafic basé sur la priorité ou pondéré au sein de cette région.

![Routage du trafic « Performance » avec distribution personnalisée du trafic au sein de la région][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Exemple 5 : paramètres de surveillance par point de terminaison

Supposons que vous utilisez Traffic Manager pour migrer en douceur le trafic d’un site web local hérité vers une nouvelle version cloud hébergée dans Azure. Pour le site hérité, vous souhaitez utiliser l’URI de la page d’accueil pour surveiller l’intégrité du site. Mais pour la nouvelle version cloud, vous implémentez une page de surveillance personnalisée (chemin d’accès « /monitor.aspx ») qui inclut des vérifications supplémentaires.

![Surveillance des points de terminaison Traffic Manager (comportement par défaut)][9]

Les paramètres d’analyse dans un profil Traffic Manager s’appliquent à tous les points de terminaison au sein d’un même profil. Avec des profils imbriqués, vous utilisez un profil enfant distinct par site pour définir des paramètres de surveillance différents.

![Surveillance des points de terminaison Traffic Manager avec paramétrage par point de terminaison][10]

## <a name="faq"></a>Forum Aux Questions

### <a name="how-do-i-configure-nested-profiles"></a>Comment configurer des profils imbriqués ?

Des profils Traffic Manager imbriqués peuvent être configurés tant à l’aide d’Azure Resource Manager, qu’à l’aide des API REST Azure classiques, d’applets de commande PowerShell et de commandes d’interface de ligne de commande Azure multiplateformes. Ils sont également pris en charge via le nouveau portail Azure. Ils ne sont pas pris en charge dans le portail Classic.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Combien de couches d’imbrication Traffic Manager prend-il en charge ?

Vous pouvez imbriquer jusqu’à 10 niveaux de profil. Les « boucles » ne sont pas autorisées.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Puis-je combiner d’autres types de point de terminaison avec des profils enfants imbriqués dans le même profil Traffic Manager ?

Oui. Il n’existe aucune restriction sur la manière de combiner des points de terminaison de différents types au sein d’un profil.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Comment le modèle de facturation s’applique-t-il aux profils imbriqués ?

Il n’y a aucun impact négatif sur la tarification en cas d’utilisation de profils imbriqués.

La facturation de Traffic Manager inclut deux composantes : des contrôles d’intégrité des points de terminaison et des millions de requêtes DNS.

* Contrôles d’intégrité des points de terminaison : aucun frais n’est facturé pour un profil enfant configuré en tant que point de terminaison dans un profil parent. La surveillance des points de terminaison dans le profil enfant est facturée comme à l’accoutumée.
* Requêtes DNS : chaque requête n’est comptabilisée qu’une seule fois. L’interrogation d’un profil parent qui retourne un point de terminaison à partir d’un profil enfant est facturée pour le profil parent uniquement.

Pour plus d’informations, voir la [page de tarification de Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Y a-t-il un impact sur les performances en cas de profils imbriqués ?

Non. Non, l’utilisation de profils imbriqués n’a aucune incidence sur les performances.

Les serveurs de noms Traffic Manager parcourent la hiérarchie de profils en interne lors du traitement de chaque requête DNS. Une requête DNS adressée à un profil parent peut recevoir une réponse DNS avec un point de terminaison d’un profil enfant. Un seul enregistrement CNAME est utilisé, que vous utilisiez un profil unique ou des profils imbriqués. Il est inutile de créer un enregistrement CNAME pour chaque profil dans la hiérarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Comment Traffic Manager calcule-t-il l’intégrité d’un point de terminaison imbriqué dans un profil parent ?

Le profil parent n’effectue pas de contrôles d’intégrité directement sur le profil enfant. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant. Ces informations sont propagées vers le haut de la hiérarchie de profils imbriqués afin de déterminer l’intégrité du point de terminaison imbriqué. Le profil parent utilise cette intégrité agrégée pour déterminer si le trafic peut être dirigé vers le profil enfant.

Le tableau suivant décrit le comportement des contrôles d’intégrité de Traffic Manager pour un point de terminaison imbriqué.

| État d’analyse des profils enfants | État d’analyse des points de terminaison parents | Remarques |
| --- | --- | --- |
| Désactivé. Le profil enfant a été désactivé. |Arrêté |L’état des points de terminaison parents est Arrêté, pas Désactivé. L’état Désactivé sert uniquement à indiquer que vous avez désactivé le point de terminaison dans le profil parent. |
| Détérioré. Au moins un point de terminaison du profil enfant a l’état Détérioré. |En ligne : le nombre de points de terminaison en ligne dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>CheckingEndpoint : le nombre de points de terminaison en ligne et CheckingEndpoint dans le profil enfant correspond au moins à la valeur de MinChildEndpoints.<BR>Détérioré : dans le cas contraire. |Le trafic est acheminé vers un point de terminaison à l’état CheckingEndpoint. Si la valeur de MinChildEndpoints est trop élevée, le point de terminaison est toujours détérioré. |
| En ligne. Au moins un point de terminaison de profil enfant est en état En ligne. Aucun point de terminaison n’est en état Détérioré. |Voir ci-dessus. | |
| CheckingEndpoints. Au moins un point de terminaison de profil enfant est en état « CheckingEndpoint ». Aucun point de terminaison n’est en état « En ligne » ou « Détérioré ». |Identique à ce qui précède. | |
| Inactif. Tous les points de terminaison du profil enfant sont en état Désactivé ou Arrêté, ou ce profil n’a aucun point de terminaison. |Arrêté | |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [fonctionnement de Traffic Manager](traffic-manager-how-traffic-manager-works.md)

En savoir plus sur la [création d’un profil Traffic Manager](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png



<!--HONumber=Jan17_HO1-->


