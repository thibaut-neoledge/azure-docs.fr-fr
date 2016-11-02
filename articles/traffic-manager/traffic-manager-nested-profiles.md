<properties 
   pageTitle="Profils Traffic Manager imbriqués | Microsoft Azure"
   description="Cet article explique la fonctionnalité des profils imbriqués d’Azure Traffic Manager"
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
   ms.date="05/25/2016"
   ms.author="sewhee" />

# Profils Traffic Manager imbriqués

Traffic Manager comprend des méthodes de routage du trafic, ce qui vous permet de contrôler la manière dont Traffic Manager choisit le point de terminaison qui doit recevoir le trafic de chaque utilisateur final. Elles sont décrites dans [Méthodes de routage de Traffic Manager](traffic-manager-routing-methods.md) et permettent à Traffic Manager de répondre aux exigences de routage du trafic les plus courantes.

Chaque profil Traffic Manager spécifie une seule méthode de routage du trafic. Toutefois, certaines applications plus complexes nécessitent parfois un routage du trafic plus sophistiqué que celui d’un unique profil Traffic Manager.

Pour prendre en charge ces applications complexes, Traffic Manager permet de combiner des profils Traffic Manager (qui sont alors *imbriqués*) pour qu’une application unique puisse tirer parti des avantages de plusieurs méthodes de routage. Les profils imbriqués vous permettent de créer des schémas de routage du trafic et de basculement plus souples et plus puissants pour répondre à des besoins de déploiements plus vastes et plus complexes.

En outre, les profils imbriqués permettent dans certains cas d’ignorer le comportement de Traffic Manager par défaut, notamment le routage du trafic au sein d’une région ou pendant un basculement lorsque vous utilisez le routage du trafic basé sur les performances.

Le reste de cette page explique à l’appui d’une série d’exemples comment les profils Traffic Manager imbriqués peuvent être utilisés dans une variété de scénarios. Et nous terminons par le Forum aux questions concernant les profils imbriqués.

## Exemple 1: combinaison de routage du trafic « performant (Performance) » et « pondéré (Weighted) »

Supposons que votre application est déployée dans plusieurs régions Azure (États-Unis de l'Ouest, Europe de l'Ouest, Asie de l'Est). Vous utilisez la méthode de routage du trafic « Performance » de Traffic Manager pour répartir le trafic vers la région la plus proche de l’utilisateur.

![Profil Traffic Manager unique][1]

Maintenant, supposons que vous souhaitiez évaluer une mise à jour de votre service pour un petit nombre d’utilisateurs avant d’effectuer un déploiement plus large. Pour ce faire, vous souhaitez utiliser la méthode de routage du trafic « Weighted », qui peut diriger une petite partie du trafic vers votre déploiement d’évaluation. Avec un seul profil, vous ne pouvez pas combiner les routages du trafic « Weighted » et « Performance ». Mais les profils imbriqués vous le permettent.

Voici comment : supposons que vous souhaitez évaluer le nouveau déploiement en Europe de l’Ouest. Vous configurez le déploiement d’évaluation en même temps que le déploiement de production existant, puis vous créez un profil Traffic Manager à l’aide de ces deux points de terminaison et la méthode de routage du trafic « Weighted ». Ajoutez ensuite ce profil « enfant » comme point de terminaison au profil « parent », qui utilise toujours la méthode de routage du trafic Performance et contient également les autres déploiements globaux comme points de terminaison.

Le schéma suivant illustre cet exemple :

![Profils Traffic Manager imbriqués][2]

De cette manière, le trafic dirigé par le biais du profil parent est réparti entre les régions comme d’habitude. En Europe de l’Ouest, vous serez dirigé le trafic entre les déploiements de production et d’évaluation en fonction de la pondération.

Notez que l’emplacement de chaque point de terminaison doit être connu lorsque le profil parent utilise la méthode de routage du trafic « Performance ». Pour les points de terminaison imbriquée, comme pour les points de terminaison externes, cet emplacement doit être spécifié dans le cadre de la configuration des points de terminaison. Sélectionnez la région Azure la plus proche de votre déploiement. Les options disponibles sont les régions Azure, car ce sont les emplacements pris en charge par la table de latence Internet. Pour plus d’informations, consultez [Traffic Manager - Méthode de routage du trafic basé sur les performances](traffic-manager-routing-methods.md#performance-traffic-routing-method).

## Exemple 2 : analyse de points de terminaison dans des profils imbriqués

Traffic Manager surveille activement l'intégrité de chaque point de terminaison de service. Si un point de terminaison défectueux est détecté, Traffic Manager dirige les utilisateurs vers d’autres points de terminaison afin de préserver la disponibilité globale de votre service. Ce comportement de surveillance et de basculement des points de terminaison s’applique à toutes les méthodes de routage du trafic. Pour plus d’informations, consultez la rubrique relative à la [surveillance des points de terminaison avec Traffic Manager](traffic-manager-monitoring.md).

Pour les profils imbriqués, certaines règles spécifiques de surveillance de point de terminaison s’appliquent. Lorsqu’un profil parent est configuré avec un profil enfant comme point de terminaison imbriqué, le parent n’effectue pas les contrôles d’intégrité de l’enfant directement. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant, et cette information est propagée dans la hiérarchie du profil imbriqué pour déterminer l’intégrité du point de terminaison imbriqué au sein du profil parent. Ce paramètre détermine si le profil parent devra diriger le trafic vers l’enfant. Vous trouverez des informations détaillées sur le calcul exact de l’intégrité du point de terminaison imbriqué dans le profil parent à partir de l’intégrité du profil enfant [ci-dessous](#faq).

En revenant à l’exemple 1, supposons que le déploiement de production en Europe de l’Ouest soit défaillant. Par défaut, le profil « enfant » dirige tout le trafic vers le déploiement d’évaluation. S’il est également défaillant, le profil parent détermine que, étant donné que tous les points de terminaison enfants ne sont pas intègres, le profil enfant ne doit pas recevoir le trafic, et il bascule le trafic entier de l’Europe de l’Ouest vers d’autres régions.

![Basculement de profil imbriqué (comportement par défaut)][3]

Cette solution peut être satisfaisante, mais il est possible que vous ne souhaitiez pas utiliser le déploiement d’évaluation comme basculement pour tout le trafic d’Europe de l’Ouest. Dans ce cas, vous préférerez basculer le trafic vers les autres régions si le déploiement échoue en Europe de l’Ouest, *quel que soit* le niveau d’intégrité du déploiement d’évaluation. La solution suivante est également possible : lorsque vous configurez le profil enfant en tant que point de terminaison dans le profil parent, vous pouvez spécifier le paramètre « MinChildEndpoints », qui détermine le nombre minimal de points de terminaison qui doivent être disponibles dans le profil enfant. En dessous de ce seuil (par défaut : 1), le profil parent prend en compte l’intégralité du profil enfant comme non disponibles et dirige le trafic vers les autres points de terminaison du profil parent.

L’exemple ci-dessous illustre : avec MinChildEndpoints défini sur 2, le profil parent détermine que le profil enfant ne doit pas recevoir le trafic et les utilisateurs sont dirigés vers les autres régions si un déploiement en Europe de l’Ouest est défaillant.

![Basculement de profil imbriqué avec « MinChildEndpoints » = 2][4]

Notez que, lorsque le profil enfant utilise la méthode de routage de trafic « Priorité (Priority) », tout le trafic vers cet enfant est reçu par un seul point de terminaison. Par conséquent, aucune valeur autre que « 1 » n’est pertinente pour le paramètre MinChildEndpoints dans ce cas.

## Exemple 3 : basculement hiérarchisé des régions dans le routage du trafic de type « Performance »

Avec un seul profil utilisant le routage du trafic « Performance », tout le trafic qui aurait été dirigé vers ce point de terminaison est réparti entre les autres points de terminaison dans toutes les régions si un point de terminaison (par exemple, Europe de l’Ouest) est défaillant. Il s’agit du comportement par défaut pour la méthode de routage du trafic « Performance », conçu pour éviter une surcharge du point de terminaison le plus proche qui entraînerait une série d’échecs en cascade.

![Routage du trafic « Performance » avec basculement par défaut][5]

Toutefois, supposons que vous préférez que le trafic de l’Europe de l’Ouest soit basculé vers les États-Unis de l'Ouest et ne soit basculé vers un autre endroit que si ces points de terminaison ne sont pas disponibles. Pour cela, vous pouvez créer un profil enfant qui utilise la méthode de routage du trafic « Priority », comme indiqué ici :

![Routage du trafic « Performance » avec basculement préférentiel][6]

Étant donné que le point de terminaison Europe de l’Ouest a une priorité plus élevée que le point de terminaison États-Unis de l'Ouest, tout le trafic est envoyé au point de terminaison Europe de l’Ouest lorsque les deux sont en ligne. En cas de défaillance du point de terminaison Europe de l’Ouest, le trafic est dirigé vers les États-Unis de l'Ouest. Le trafic de l’Europe de l’Ouest ne serait dirigé vers l’Asie de l'Est que si le point de terminaison États-Unis de l'Ouest est également défaillant.

Vous pouvez répéter ce schéma pour toutes les régions, en remplaçant les 3 points de terminaison dans le profil parent par 3 profils enfants ayant chacun une séquence de priorité de basculement.

## Exemple 4 : contrôle du routage du trafic de type « Performance » entre plusieurs points de terminaison dans la même région

Supposons que la méthode de routage du trafic « Performance » est utilisée dans un profil qui a plus d’un point de terminaison dans une région spécifique, par exemple les États-Unis de l'Ouest. Par défaut, le trafic dirigé vers cette région est distribué uniformément entre tous les points de terminaison disponibles dans cette région.

![Routage du trafic « Performance » avec distribution du trafic en région (comportement par défaut)][7]

Cette valeur par défaut peut être modifiée à l’aide de profils Traffic Manager imbriqués. Au lieu d’ajouter plusieurs points de terminaison dans les États-Unis de l'Ouest, ces points de terminaison peuvent être placés entre un profil distinct, et le profil enfant ajouté au parent comme seul point de terminaison dans les États-Unis de l'Ouest. Les paramètres du profil enfant peuvent être ensuite utilisés pour contrôler la distribution du trafic vers les États-Unis de l'Ouest, ce qui permet (par exemple) un routage du trafic basé sur la priorité ou la pondération au sein de cette région.

![Routage du trafic « Performance » avec distribution du trafic en région personnalisée][8]

## Exemple 5 : paramètres de surveillance par point de terminaison

Supposons que vous utilisez Traffic Manager pour migrer en toute simplicité le trafic entre un site web local hérité et une nouvelle version cloud hébergée dans Azure. Pour le site hérité, vous utilisez la page d’accueil (chemin d’accès « / ») pour surveiller l’intégrité du site. Mais, pour la nouvelle version cloud, vous mettez en œuvre une page de surveillance personnalisée qui inclut des vérifications supplémentaires (chemin d’accès « / monitor.aspx »).

![Surveillance des points de terminaison Traffic Manager (comportement par défaut)][9]

Les paramètres de surveillance dans un profil Traffic Manager s’appliquent à tous les points de terminaison au sein du profil, ce qui signifie que vous devez utiliser auparavant le même chemin d’accès sur les deux sites. Grâce aux profils Traffic Manager imbriqués, vous pouvez désormais utiliser un profil enfant par site pour définir différents paramètres de surveillance par site :

![Surveillance des points de terminaison Traffic Manager avec paramétrage par point de terminaison][10]

## Forum Aux Questions

### Comment configurer des profils imbriqués ?

Les profils Traffic Manager imbriqués peuvent être configurés à l’aide d’Azure Resource Manager (ARM) et des API REST Azure Service Management (ASM), des applets de commande PowerShell et des commandes d’interface de ligne de commande Azure interplateforme. Ils sont également pris en charge par le portail Azure, mais ils ne sont pas pris en charge dans le portail « Classic ».

### Combien de couches d’imbrication Traffic Manager prend-il en charge ?
Vous pouvez imbriquer jusqu’à 10 niveaux de profil. Les « boucles » ne sont pas autorisées.

### Puis-je combiner d’autres types de point de terminaison avec des profils enfants imbriqués dans le même profil Traffic Manager ?

Oui. Il n’existe aucune restriction sur la manière de combiner des points de terminaison de différents types au sein d’un profil.

### Comment le modèle de facturation s’applique-t-il aux profils imbriqués ?

Il n’y a aucun impact négatif sur la tarification en cas d’utilisation de profils imbriqués.

La facturation de Traffic Manager comporte deux composants : les contrôles d’intégrité des points de terminaison et des millions de requêtes DNS (pour plus d’informations, consultez notre [page de tarification](https://azure.microsoft.com/pricing/details/traffic-manager/).) Voici comment ce modèle s’applique aux profils imbriqués :

- Contrôles d’intégrité des points de terminaison : aucun frais n’est facturé pour un profil enfant configuré en tant que point de terminaison dans un profil parent. Les points de terminaison dans le profil enfant qui surveillent les services sous-jacents sont facturés de la manière habituelle.

- Requêtes DNS : chaque requête est comptabilisée une seule fois. L’interrogation d’un profil parent qui retourne un point de terminaison depuis un profil enfant est facturée sur le profil parent uniquement.

### Y a-t-il un impact sur les performances en cas de profils imbriqués ?

Non, il n’a aucun impact sur les performances en cas d’utilisation de profils imbriqués.

Les serveurs de noms Traffic Manager parcourent la hiérarchie de profils en interne lors du traitement de chaque requête DNS, donc une requête DNS sur un profil parent peut recevoir une réponse DNS avec un point de terminaison d’un profil enfant.

Un seul enregistrement CNAME est donc utilisé, le même que lorsque vous utilisez un seul profil Traffic Manager. Une chaîne d’enregistrements CNAME (une pour chaque profil dans la hiérarchie), n’est **pas** nécessaire, et par conséquent aucune altération des performances n’est constatée.

### Comment Traffic Manager calcule l’intégrité d’un point de terminaison imbriqué dans un profil parent sur la base de l’intégrité du profil enfant ?

Lorsqu’un profil parent est configuré avec un profil enfant comme point de terminaison imbriqué, le parent n’effectue pas les contrôles d’intégrité de l’enfant directement. Au lieu de cela, l’intégrité des points de terminaison du profil enfant est utilisée pour calculer l’intégrité globale du profil enfant, et cette information est propagée dans la hiérarchie du profil imbriqué pour déterminer l’intégrité du point de terminaison imbriqué au sein du profil parent. Ce paramètre détermine si le profil parent devra diriger le trafic vers l’enfant.

Le tableau suivant décrit le comportement des contrôles d’intégrité Traffic Manager pour un point de terminaison imbriqué dans un profil parent pointant vers un profil enfant.

|État d’analyse des profils enfants|État d’analyse des points de terminaison parents|Remarques|
|---|---|---|
|Désactivé. Le profil enfant a été désactivé par l’utilisateur.|Arrêté|L’état des points de terminaison parents est Arrêté, pas Désactivé. L’état Désactivé sert uniquement à indiquer que vous avez désactivé le point de terminaison dans le profil parent.|
|Détérioré. Au moins un point de terminaison du profil enfant a l’état Détérioré.|En ligne : le nombre de points de terminaison en ligne dans le profil enfant correspond au moins à la valeur de MinChildEndpoints. CheckingEndpoint : le nombre de points de terminaison en ligne et CheckingEndpoint dans le profil enfant correspond au moins à la valeur de MinChildEndpoints. Détérioré : dans le cas contraire.|Le trafic est acheminé vers un point de terminaison à l’état CheckingEndpoint. Si la valeur définie de MinChildEndpoints est trop élevée, le point de terminaison sera toujours détérioré.|
|En ligne. au moins un point de terminaison du profil enfant a l’état En ligne, et aucun n’a l’état Détérioré.|Voir ci-dessus.||
|CheckingEndpoints. au moins un point de terminaison du profil enfant a l’état CheckingEndpoint. Aucun n’a l’état En ligne ou Détérioré.|Identique à ce qui précède.||
|Inactif. tous les points de terminaison du profil enfant ont l’état Désactivé ou Arrêté, ou ce profil n’inclut aucun point de terminaison.|Arrêté||


## Étapes suivantes

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

<!---HONumber=AcomDC_0824_2016-->