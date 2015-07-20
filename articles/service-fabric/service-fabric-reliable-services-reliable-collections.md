<properties
   pageTitle="Collections fiables"
   description="Les Collections fiables vous permettent d'écrire des applications cloud hautement disponibles, évolutives et à faible latence."
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="06/09/2015"
   ms.author="mcoskun"/>

# Collections fiables

Les Collections fiables vous permettent d'écrire des applications cloud hautement disponibles, évolutives et à faible latence comme si vous écriviez des applications pour ordinateur unique. Les classes dans l'espace de noms `Microsoft.ServiceFabric.Data.Collections` fournissent un ensemble de collections prêtes à l'emploi qui rendent automatiquement votre état hautement disponible. Il suffit aux développeurs de programmer les API de Collection fiable et de laisser les Collections fiables gérer l'état répliqué et local.

La principale différence entre les Collections fiables et d'autres technologies de haute disponibilité (comme Redis, le service Table Azure et le service File d'attente Azure) est que l'état est conservé localement dans l'instance de service tout en étant également rendu hautement disponible. Cela signifie que :

1. Toutes les lectures sont locales, ce qui entraîne des lectures à latence faible et à débit élevé.
2. Toutes les écritures entraînent un nombre minimal d'entrées et sorties réseau, ce qui résulte en des écritures à latence faible et à débit élevé.

![Image de l'évolution des collections.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Les Collections fiables peuvent être considérées comme l'évolution naturelle des classes `System.Collections` : un nouveau jeu de collections qui sont conçues pour le cloud et les applications pour plusieurs ordinateurs sans accroître la complexité pour les développeurs. Par conséquent, elles sont :

1. Répliquées : les modifications d'état sont répliquées pour une haute disponibilité.
2. Persistantes : les données sont conservées sur le disque pour la durabilité contre les pannes à grande échelle (par exemple, une panne de courant dans un centre de données).
3. Asynchrones : les API sont asynchrones afin de s'assurer que les threads ne sont pas bloqués en cas d'entrées/sorties.
4. Transactionnelles : les API utilisent l'abstraction de transactions pour vous permettre de gérer facilement plusieurs Collections fiables au sein d'un service.

Les Collections fiables fournissent des garanties de forte cohérence instantanée afin de faciliter le raisonnement sur l'état de l'application. La forte cohérence est obtenue en s'assurant que la transaction n'est validée comme terminée que lorsque l'intégralité de la transaction a été appliquée à un quorum de réplicas, y compris l'instance principale. Pour obtenir une cohérence plus faible, l'application peut accuser réception au client / demandeur avant la validation asynchrone.

Les API de Collections fiables sont une évolution des API de collections simultanées (trouvées dans l'espace de noms `System.Collections.Concurrent`) :

1. Asynchrones : renvoie une tâche car, contrairement aux Collections fiables, les opérations sont répliquées et conservées.
2. Aucun paramètre de sortie : utilise `ConditionalResult<T>` pour renvoyer un paramètre booléen et une valeur au lieu de paramètres. `ConditionalResult<T>` est similaire à `Nullable<T>` mais ne nécessite pas que T soit une structure.
3. Transactions : utilise un objet de transaction pour permettre à l'utilisateur de regrouper des actions sur plusieurs Collections fiables dans une transaction.

Aujourd'hui, `Microsoft.ServiceFabric.Data.Collections` contient deux collections :

1. [Dictionnaire fiable](https://msdn.microsoft.com/library/azure/dn971511.aspx) : représente une collection répliquée, transactionnelle et asynchrone de paires clé/valeur. Semblable à `ConcurrentDictionary`, la clé et la valeur peuvent être de n'importe quel type.
2. [File d'attente fiable](https://msdn.microsoft.com/library/azure/dn971527.aspx) : représente une file d'attente FIFO stricte, répliquée, transactionnelle et asynchrone. Semblable à `ConcurrentQueue`, la valeur peut être de n'importe quel type.

## Niveaux d'isolement
Le niveau d'isolement est une mesure du degré d'isolement obtenu. L'isolement signifie qu'une transaction se comporte comme elle le ferait dans un système qui n'autorise qu'une seule transaction en cours à un moment donné.

Les Collections fiables choisissent automatiquement le niveau d'isolement à utiliser pour une opération de lecture donnée en fonction de l'opération et du rôle du réplica.

Il existe deux niveaux d'isolement pris en charge dans les Collections fiables :

- **Lecture renouvelée** : « spécifie que les instructions ne peuvent pas lire les données qui ont été modifiées mais pas encore validées par d'autres transactions et qu'aucune autre transaction ne peut modifier des données qui ont été lues par la transaction actuelle avant la fin de la transaction actuelle. (https://msdn.microsoft.com/fr-fr/library/ms173763.aspx)) »
- **Instantané** : « spécifie que les données lues par toute instruction dans une transaction sont la version transactionnellement cohérente des données qui existaient au début de la transaction. La transaction ne peut reconnaître que les modifications de données qui ont été validées avant le début de la transaction. Les modifications de données effectuées par d'autres transactions après le début de la transaction actuelle ne sont pas visibles pour les instructions qui s’exécutent dans la transaction actuelle. C'est comme si les instructions dans une transaction obtenaient un instantané des données validées telles qu'elles existaient au début de la transaction. (https://msdn.microsoft.com/fr-fr/library/ms173763.aspx)"

Le Dictionnaire fiable et la File d'attente fiable prennent en charge le protocole Read Your Writes. En d'autres termes, toute écriture dans une transaction sera visible pour une lecture suivante appartenant à la même transaction.

### Dictionnaire fiable
| Fonctionnement \\ Rôle | Primaire | Secondaire |
| --------------------- | :--------------- | :--------------- |
| Lecture d'une seule entité | Lecture renouvelée | Instantané |
| Énumération \\ Décompte | Instantané | Instantané |

### File d'attente fiable
| Fonctionnement \\ Rôle | Primaire | Secondaire |
| --------------------- | :--------------- | :--------------- |
| Lecture d'une seule entité | Instantané | Instantané |
| Énumération \\ Décompte | Instantané | Instantané |

## Modèle de persistance
Le Gestionnaire d'état fiable et les Collections fiables suivent un modèle de persistance appelé Journal et Point de contrôle. Il s'agit d'un modèle où chaque changement d'état est enregistré sur le disque et appliqué uniquement en mémoire. L'état complet lui-même n'est conservé qu'occasionnellement (également appelé Point de contrôle). L'avantage qu'il fournit est le suivant :

- Les deltas sont transformés en écritures séquentielles en mode ajouter uniquement sur disque pour améliorer les performances.

Pour mieux comprendre le modèle de journal et de point de contrôle, penchons-nous d'abord sur le scénario de disque infini. Le Gestionnaire d'état fiable enregistre chaque opération avant qu'elle ne soit répliquée. Ceci permet à la Collection fiable d'appliquer uniquement l'opération en mémoire. Dans la mesure où les journaux sont conservés, même lorsque le réplica échoue et doit être redémarré, le Gestionnaire d'état fiable possède suffisamment d'informations dans ses journaux pour recréer toutes les opérations perdues par le réplica. Étant donné que le disque est infini, les enregistrements du journal n'ont jamais besoin d'être supprimés et la Collection fiable ne doit gérer que l'état en mémoire.

Maintenant, examinons le scénario de disque limité. À un moment donné, le Gestionnaire d'état fiable manquera d'espace disque. Avant que cela n'arrive, le Gestionnaire d'état fiable doit tronquer son journal pour accueillir les enregistrements plus récents. Il demande alors aux Collections fiables de contrôler leur état en mémoire. La responsabilité de la Collection fiable est de conserver son état jusqu'à ce point. Une fois que les Collections fiables ont terminé leur contrôle, le Gestionnaire d'état fiable peut tronquer le journal pour libérer de l'espace disque. Ainsi, lorsque le réplica doit être redémarré, les Collections fiables récupéreront leur état au point de contrôle et le Gestionnaire d'état fiable récupérera et lira toutes les modifications d'état qui se sont produites depuis le point de contrôle.

## Verrouillage
Dans les Collections fiables, toutes les transactions ont deux phases : une transaction ne libère pas les verrous qu'elle a acquis jusqu'à ce que la transaction se termine avec un abandon ou une validation.

Les Collections fiables prennent toujours des verrous exclusifs. Pour les lectures, le verrouillage dépend de plusieurs facteurs. Toute opération de lecture effectuée à l'aide de l'isolement instantané n'est pas verrouillée. Toute opération de lecture renouvelée utilise par défaut des verrous partagés. Toutefois, pour toute opération de lecture qui prend en charge la lecture renouvelée, l'utilisateur peut demander un verrou de mise à jour au lieu du verrou partagé. Le verrou de mise à jour est un verrou asymétrique utilisé pour éviter une forme courante de blocage qui se produit lorsque plusieurs transactions verrouillent les ressources pour une mise à jour éventuelle ultérieurement.

Vous trouverez ci-dessous la matrice de compatibilité de verrouillage :

| Requête \\ Accordé | Aucun | Partagé | Mettre à jour | Exclusif |
| ----------------- | :----------- | :----------- | :---------- | :----------- |
| Partagé | Aucun conflit | Aucun conflit | Conflit | Conflit |
| Mettre à jour | Aucun conflit | Aucun conflit | Conflit | Conflit |
| Exclusif | Aucun conflit | Conflit | Conflit | Conflit |

Notez que l'argument de délai d'attente dans les API de Collections fiables est utilisé comme détection de blocage. Par exemple, deux transactions (T1 et T2) essayent de lire et de mettre à jour K1. Elles peuvent être bloquées, car elles ont toutes deux le verrou partagé. Dans ce cas, l'une ou les deux opérations expirent.

Notez que le scénario de blocage ci-dessus est un bon exemple illustrant comment le verrou de mise à jour peut empêcher les blocages.

## Recommandations

- **NE MODIFIEZ PAS** un objet de type personnalisé renvoyé par les opérations de lecture (par exemple, `TryPeekAsync` ou `TryGetAsync`). Les Collections fiables, comme les Collections simultanées, renvoient une référence aux objets et non une copie.
- **EXÉCUTEZ** une copie complète de l'objet renvoyé de type personnalisé avant de le modifier. Étant donné que les structures et les types intégrés ont une valeur de passage, vous n'avez pas besoin d'en effectuer une copie complète.
- **N'UTILISEZ PAS** `TimeSpan.MaxValue` pour des délais d'attente. Les délais d'attente doivent être utilisés pour détecter les blocages.
- **NE CRÉEZ PAS** une transaction au sein de l'instruction `using` d'une autre transaction, car cela peut provoquer un blocage.

Voici quelques points à retenir :

- Le délai d'attente par défaut est de 4 secondes pour toutes les API de Collections fiables. La plupart des utilisateurs ne doivent pas remplacer ce délai.
- Le jeton d'annulation par défaut est `CancellationToken.None` dans toutes les API de Collections fiables.
- Les énumérations sont un instantané cohérent au sein d'une collection. Toutefois, les énumérations de plusieurs collections ne sont pas cohérentes d'une collection à l'autre.
- Pour obtenir un haut niveau de disponibilité pour les Collections fiables, chaque service doit avoir au moins une taille de jeu de réplicas cible minimum égale à 3.

## Étapes suivantes

- [Démarrage rapide des Services fiables](service-fabric-reliable-services-quick-start.md)
- [Prise en main des services API Web de Fabric Service](service-fabric-reliable-services-communication-webapi.md)
- [Utilisation avancée du modèle de programmation de services fiables](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)
- [Référence du développeur pour les Collections fiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)
 

<!---HONumber=July15_HO2-->