<properties
   pageTitle="Collections fiables | Microsoft Azure"
   description="Les services avec état de Service Fabric fournissent des collections fiables qui vous permettent d’écrire des applications cloud hautement disponibles, évolutives et à faible latence."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/14/2016"
   ms.author="mcoskun"/>

# Introduction aux Collections fiables dans les services avec état d’Azure Service Fabric

Les Collections fiables vous permettent d’écrire des applications cloud hautement disponibles, évolutives et à faible latence comme si vous écriviez des applications informatiques uniques. Les classes dans l’espace de noms **Microsoft.ServiceFabric.Data.Collections** fournissent un ensemble de collections prêtes à l’emploi qui rendent automatiquement votre état hautement disponible. Les développeurs doivent programmer uniquement les API de Collection fiable et laisser les Collections fiables gérer l’état répliqué et local.

La principale différence entre les Collections fiables et d'autres technologies de haute disponibilité (comme Redis, le service Table Azure et le service File d'attente Azure) est que l'état est conservé localement dans l'instance de service tout en étant également rendu hautement disponible. Cela signifie que :

- Toutes les lectures sont locales, ce qui entraîne des lectures à faible latence et à débit élevé.
- Toutes les écritures induisent un nombre minimal d’entrées et sorties réseau, ce qui entraîne des écritures à latence faible et à débit élevé.

![Image de l'évolution des collections.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Les Collections fiables peuvent être considérées comme l’évolution naturelle des classes **System.Collections** : un nouveau jeu de collections qui sont conçues pour les applications cloud et les applications pour plusieurs ordinateurs sans accroître la complexité pour les développeurs. En tant que telles, les Collections fiables sont :

- Répliquées : les modifications d'état sont répliquées pour une haute disponibilité.
- Persistantes : les données sont conservées sur le disque pour la durabilité contre les pannes à grande échelle (par exemple, une panne de courant dans un centre de données).
- Asynchrones : les API sont asynchrones afin de s’assurer que les threads ne sont pas bloqués en cas d’entrées/sorties.
- Transactionnelles : les API utilisent l’abstraction de transactions pour vous permettre de gérer facilement plusieurs Collections fiables au sein d’un service.

Les Collections fiables fournissent des garanties de forte cohérence instantanée afin de faciliter le raisonnement sur l'état de l'application. La forte cohérence est obtenue en s’assurant que la transaction n’est validée comme terminée que lorsque l’intégralité de la transaction a été enregistrée dans un quorum majoritaire de réplicas, y compris le réplica principal. Pour obtenir une cohérence plus faible, les applications peuvent accuser réception au client/demandeur avant la validation asynchrone.

Les API de Collections fiables sont une évolution des API de collections simultanées (trouvées dans l’espace de noms **System.Collections.Concurrent**) :

- Asynchrones : renvoie une tâche, car contrairement aux collections simultanées, les opérations sont répliquées et conservées.
- Aucun paramètre de sortie : utilise `ConditionalValue<T>` pour renvoyer un paramètre booléen et une valeur au lieu de paramètres. `ConditionalValue<T>` est similaire à `Nullable<T>` mais ne nécessite pas que T soit une structure.
- Transactions : utilise un objet de transaction pour permettre à l'utilisateur de regrouper des actions sur plusieurs Collections fiables dans une transaction.

Actuellement, **Microsoft.ServiceFabric.Data.Collections** contient deux collections :

- [Dictionnaire fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx) : représente une collection répliquée, transactionnelle et asynchrone de paires clé/valeur. Semblables à celles de **ConcurrentDictionary**, la clé et la valeur peuvent être de tout type.
- [File d’attente fiable](https://msdn.microsoft.com/library/azure/dn971527.aspx) : représente une file d’attente FIFO stricte, répliquée, transactionnelle et asynchrone. Semblable à celle de **ConcurrentQueue**, la valeur peut être de tout type.

## Niveaux d'isolement
Le niveau d’isolement est une mesure du degré d’isolement obtenu. L’isolement signifie qu’une transaction se comporte comme elle le ferait dans un système qui n’autorise qu’une seule transaction en cours à un moment donné.

Les Collections fiables choisissent automatiquement le niveau d'isolement à utiliser pour une opération de lecture donnée en fonction de l'opération et du rôle du réplica.

Il existe deux niveaux d'isolement pris en charge dans les Collections fiables :

- **Lecture renouvelée** : spécifie que les instructions ne peuvent pas lire les données qui ont été modifiées, mais pas encore validées par d’autres transactions et qu’aucune autre transaction ne peut modifier des données qui ont été lues par la transaction actuelle avant la fin de celle-ci. Pour plus d’informations, consultez la page [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).
- **Instantané** : spécifie que les données lues par toute instruction dans une transaction sont la version transactionnellement cohérente des données qui existaient au début de la transaction. La transaction ne peut reconnaître que les modifications de données qui ont été validées avant son démarrage. Les modifications de données effectuées par d'autres transactions après le début de la transaction actuelle ne sont pas visibles pour les instructions qui s’exécutent dans la transaction actuelle. C’est comme si les instructions d’une transaction obtenaient un instantané des données validées telles qu’elles existaient au début de la transaction. Les instantanés sont cohérents sur les Collections fiables. Pour plus d’informations, consultez la page [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).

Le Dictionnaire fiable et la File d'attente fiable prennent en charge le protocole Read Your Writes. En d'autres termes, toute écriture dans une transaction sera visible pour une lecture suivante appartenant à la même transaction.

### Dictionnaire fiable
| Fonctionnement \\ Rôle | Primaire | Secondaire |
| --------------------- | :--------------- | :--------------- |
| Lecture d’une seule entité | Lecture renouvelée | Instantané |
| Énumération \\ Décompte | Instantané | Instantané |

### File d'attente fiable
| Fonctionnement \\ Rôle | Primaire | Secondaire |
| --------------------- | :--------------- | :--------------- |
| Lecture d’une seule entité | Instantané | Instantané |
| Énumération \\ Décompte | Instantané | Instantané |

## Modèle de persistance
Le gestionnaire d’état fiable et les Collections fiables suivent un modèle de persistance appelé Journal et Point de contrôle. Il s’agit d’un modèle où chaque changement d’état est enregistré sur le disque et appliqué uniquement en mémoire. L’état complet proprement dit n’est conservé qu’occasionnellement (également appelé Point de contrôle). L’avantage fourni est le suivant :

- Les deltas sont transformés en écritures séquentielles en mode ajouter uniquement sur disque pour améliorer les performances.

Pour mieux comprendre le modèle de journal et de point de contrôle, penchons-nous d’abord sur le scénario de disque infini. Le gestionnaire d’état fiable enregistre chaque opération avant qu’elle ne soit répliquée. Cela permet à la Collection fiable d’appliquer uniquement l’opération en mémoire. Dans la mesure où les journaux sont conservés, même lorsque le réplica échoue et doit être redémarré, le Gestionnaire d'état fiable possède suffisamment d'informations dans ses journaux pour recréer toutes les opérations perdues par le réplica. Étant donné que le disque est infini, les enregistrements du journal n’ont jamais besoin d’être supprimés, et la Collection fiable ne doit gérer que l’état en mémoire.

Maintenant, examinons le scénario de disque limité. À un moment donné, le gestionnaire d’état fiable manquera d’espace disque. Avant que cela n’arrive, le gestionnaire d’état fiable doit tronquer son journal pour accueillir les enregistrements plus récents. Il demande alors aux Collections fiables de contrôler leur état en mémoire sur disque. La responsabilité de la Collection fiable est de conserver son état jusqu'à ce point. Une fois que les Collections fiables ont terminé leur contrôle, le Gestionnaire d'état fiable peut tronquer le journal pour libérer de l'espace disque. Ainsi, lorsque le réplica doit être redémarré, les Collections fiables récupéreront leur état au point de contrôle et le Gestionnaire d'état fiable récupérera et lira toutes les modifications d'état qui se sont produites depuis le point de contrôle.

## Verrouillage
Dans les Collections fiables, toutes les transactions comptent deux phases : une transaction ne libère pas les verrous qu’elle a acquis tant qu’elle ne s’est pas terminée avec un abandon ou une validation.

Les Collections fiables prennent toujours des verrous exclusifs. Pour les lectures, le verrouillage dépend de plusieurs facteurs. Les opérations de lecture effectuées à l’aide de l’isolement d’instantané ne sont pas verrouillées. Les opérations de lecture renouvelée utilisent par défaut des verrous partagés. Toutefois, pour toutes les opérations de lecture qui prennent en charge la lecture renouvelée, l’utilisateur peut demander un verrou de mise à jour au lieu du verrou partagé. Un verrou de mise à jour est un verrou asymétrique utilisé pour éviter une forme courante de blocage qui se produit lorsque plusieurs transactions verrouillent les ressources pour des mises à jour éventuelles ultérieures.

Vous trouverez ci-dessous la matrice de compatibilité de verrouillage :

| Requête \\ Accordé | Aucun | Partagé | Mettre à jour | Exclusif |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Partagé | Aucun conflit | Aucun conflit | Conflit | Conflit |
| Mettre à jour | Aucun conflit | Aucun conflit | Conflit | Conflit |
| Exclusif | Aucun conflit | Conflit | Conflit | Conflit |

Notez qu’un argument de délai d’expiration dans les API de Collections fiables est utilisé comme détection de blocage. Par exemple, deux transactions (T1 et T2) essayent de lire et de mettre à jour K1. Elles peuvent être bloquées, car elles ont toutes deux le verrou partagé. Dans ce cas, l’une ou les deux opérations arrivent à expiration.

Notez que le scénario de blocage ci-dessus est un exemple illustrant parfaitement en quoi le verrou de mise à jour peut empêcher les blocages.

## Recommandations

- Ne modifiez pas un objet de type personnalisé renvoyé par les opérations de lecture (par exemple, `TryPeekAsync` ou `TryGetValueAsync`). Les Collections fiables, comme les Collections simultanées, renvoient une référence aux objets et non une copie.
- Exécutez une copie complète de l’objet renvoyé de type personnalisé avant de le modifier. Comme les structures et les types intégrés ont une valeur de passage, vous n’avez pas besoin d’en effectuer une copie complète.
- N’utilisez pas `TimeSpan.MaxValue` pour les délais d’attente. Les délais d’expiration doivent être utilisés pour détecter des blocages.
- N’utilisez pas une transaction une fois qu’elle a été validée, abandonnée ou supprimée.
- Les énumérateurs construits à l’intérieur d’une étendue de transaction ne doivent pas servir en dehors de cette étendue.
- Ne créez pas une transaction au sein de l’instruction `using` d’une autre transaction, car cela peut provoquer des blocages.
- Assurez-vous que votre implémentation de `IComparable<TKey>` est correcte. Le système en dépend pour la fusion des points de contrôle.
- Envisagez d’utiliser la fonctionnalité de sauvegarde et de restauration pour bénéficier de la récupération d’urgence.

Voici quelques points à retenir :

- Le délai d’expiration par défaut est de 4 secondes pour toutes les API de Collections fiables. La plupart des utilisateurs ne doivent pas remplacer ce délai.
- Le jeton d'annulation par défaut est `CancellationToken.None` dans toutes les API de Collections fiables.
- Le paramètre de type de clé (*TKey*) pour un Dictionnaire fiable doit implémenter correctement `GetHashCode()` et `Equals()`. Les clés doivent être immuables.
- Pour obtenir un haut niveau de disponibilité pour les Collections fiables, chaque service doit avoir au moins une taille de jeu de réplicas cible minimum égale à 3.
- Les opérations de lecture sur le secondaire peuvent lire des versions qui ne sont pas validées dans le quorum. Cela signifie qu’une version des données lue à partir d’un seul secondaire peut présenter une progression erronée. Bien sûr, les lectures à partir du principal sont toujours stables : la progression n’est jamais erronée.

## Étapes suivantes

- [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Sauvegarde et restauration de Reliable Services (récupération d’urgence)](service-fabric-reliable-services-backup-restore.md)
- [Configuration du Gestionnaire d’état fiable](service-fabric-reliable-services-configuration.md)
- [Prise en main des services API Web de Fabric Service](service-fabric-reliable-services-communication-webapi.md)
- [Utilisation avancée du modèle de programmation de services fiables](service-fabric-reliable-services-advanced-usage.md)
- [Référence du développeur pour les Collections fiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0622_2016-->