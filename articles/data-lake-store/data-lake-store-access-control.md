---
title: "Vue d’ensemble du contrôle d’accès dans Data Lake Store | Microsoft Docs"
description: "Comprendre le contrôle d’accès dans Azure Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d16f8c09-c954-40d3-afab-c86ffa8c353d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/02/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9637a4dfeaf3d3f95ccdb4bbc5d1f96ec08b6dad


---
# <a name="access-control-in-azure-data-lake-store"></a>Contrôle d’accès dans Azure Data Lake Store
Data Lake Store met en œuvre un modèle de contrôle d’accès dérivé de HDFS et, de ce fait, du modèle de contrôle d’accès POSIX. Cet article présente les notions de base du modèle de contrôle d’accès pour Data Lake Store. Pour en savoir plus sur le modèle de contrôle d’accès HDFS, reportez-vous au [guide des autorisations HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listes de contrôle d’accès sur les fichiers et dossiers
Il existe deux types de listes de contrôle d’accès (ACL) : les **ACL d’accès** et les **ACL par défaut**.

* **ACL d’accès** : elles contrôlent l’accès à un objet. Les fichiers et les dossiers ont tous des ACL d’accès.
* **ACL par défaut** : « modèle » d’ACL associé à un dossier, qui détermine les ACL d’accès pour tous les éléments enfants créés dans ce dossier. Les fichiers n’ont pas d’ACL par défaut.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

Les ACL d’accès et les ACL par défaut ont la même structure.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

> [!NOTE]
> La modification de l’ACL par défaut d’un parent n’affecte pas l’ACL d’accès ni l’ACL par défaut des éléments enfants qui existent déjà.
> 
> 

## <a name="users-and-identities"></a>Utilisateurs et identités
Chaque fichier et dossier dispose d’autorisations distinctes pour ces identités :

* L’utilisateur propriétaire du fichier
* Le groupe propriétaire
* Les utilisateurs nommés
* Les groupes nommés
* Tous les autres utilisateurs

Les identités des utilisateurs et des groupes sont des identités Azure Active Directory (AAD). Par conséquent, sauf indication contraire, un « utilisateur », dans le contexte de Data Lake Store, peut correspondre soit à un utilisateur AAD, soit à un groupe de sécurité AAD.

## <a name="permissions"></a>Autorisations
Les autorisations sur un objet de système de fichiers sont **Lecture**, **Écriture** et **Exécution**. Elles peuvent être utilisées sur les fichiers et les dossiers comme l’indique le tableau ci-dessous.

|  | Fichier | Dossier |
| --- | --- | --- |
| **Lecture (R)** |Permet de lire le contenu d’un fichier |Requiert les autorisations **Lecture** et **Exécution** pour répertorier le contenu du dossier. |
| **Écriture (W)** |Permet d’écrire ou d’ajouter du contenu dans un fichier |Requiert les autorisations **Écriture et Exécution** pour créer des éléments enfants dans un dossier. |
| **Exécution (X)** |Ne signifie rien dans le contexte de Data Lake Store |Requis pour parcourir les éléments enfants d’un dossier. |

### <a name="short-forms-for-permissions"></a>Formes abrégées des autorisations
**RWX** sert à indiquer **Lecture + Écriture + Exécution**. Il existe une forme numérique plus condensée dans laquelle **Lecture = 4**, **Écriture = 2** et **Exécution = 1**. Les autorisations sont représentées par la somme de ces chiffres. Vous trouverez quelques exemples ci-dessous.

| Forme numérique | Forme abrégée | Signification |
| --- | --- | --- |
| 7 |RWX |Lecture + Écriture + Exécution |
| 5 |R-X |Lecture + Exécution |
| 4 |R-- |Lire |
| 0 |--- || Aucune autorisation |

### <a name="permissions-do-not-inherit"></a>Les autorisations ne se transmettent pas en héritage
Dans le modèle de style POSIX utilisé par Data Lake Store, les autorisations d’un élément sont stockées sur l’élément lui-même. En d’autres termes, les autorisations d’un élément ne peuvent pas être héritées des éléments parents.

## <a name="common-scenarios-related-to-permissions"></a>Scénarios courants liés aux autorisations
Voici quelques scénarios courants pour comprendre les autorisations nécessaires pour effectuer certaines opérations sur un compte Data Lake Store.

### <a name="permissions-needed-to-read-a-file"></a>Autorisations nécessaires pour lire un fichier
![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Pour le fichier à lire : l’appelant doit disposer de l’autorisation **Lecture**
* Pour tous les dossiers dans la structure de dossiers contenant le fichier : l’appelant doit disposer de l’autorisation **Exécution**

### <a name="permissions-needed-to-append-to-a-file"></a>Autorisations nécessaires pour ajouter du contenu à un fichier
![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Pour le fichier auquel ajouter du contenu : l’appelant doit disposer de l’autorisation **Écriture**
* Pour tous les dossiers qui contiennent le fichier : l’appelant doit disposer de l’autorisation **Exécution**

### <a name="permissions-needed-to-delete-a-file"></a>Autorisations nécessaires pour supprimer un fichier
![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Pour le dossier parent : l’appelant doit disposer des autorisations **Écriture + Exécution**
* Pour tous les autres dossiers du chemin du fichier : l’appelant doit disposer de l’autorisation **Exécution**

> [!NOTE]
> L’autorisation d’écrire sur le fichier n’est pas nécessaire pour supprimer le fichier, tant que les deux conditions ci-dessus sont remplies.
> 
> 

### <a name="permissions-needed-to-enumerate-a-folder"></a>Autorisations nécessaires pour énumérer un dossier
![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Pour le dossier à énumérer : l’appelant doit disposer des autorisations **Lecture + Exécution**
* Pour tous les dossiers parents : l’appelant doit disposer de l’autorisation **Exécution**

## <a name="viewing-permissions-in-the-azure-portal"></a>Affichage des autorisations dans le portail Azure
À partir du panneau **Explorateur de données** du compte Data Lake Store, cliquez sur **Accès** pour afficher les ACL d’un fichier ou d’un dossier. Dans la capture d’écran ci-dessous, cliquez sur Accès pour afficher les ACL du dossier **catalogue** du compte **mydatastore**.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Dans ce panneau, la section supérieure affiche une vue d’ensemble des autorisations dont vous disposez (dans la capture d’écran, l’utilisateur se nomme Bob). En dessous, les autorisations d’accès sont affichées.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Cliquez sur **Avancé** pour afficher la vue plus avancée où figurent les concepts d’ACL par défaut, de masque et de super utilisateur.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>Les super utilisateurs
Les super utilisateurs sont ceux qui ont le plus de droits parmi tous les utilisateurs de Data Lake Store. Un super utilisateur :

* dispose des autorisations RWX sur **tous** les fichiers et dossiers ;
* peut modifier les autorisations de n’importe quel fichier ou dossier ;
* peut modifier l’utilisateur propriétaire ou le groupe propriétaire d’un fichier ou d’un dossier.

Dans Azure, il existe plusieurs rôles Azure pour les comptes Data Lake Store :

* Propriétaires
* Contributeurs
* Lecteurs
* etc.

Toutes les personnes ayant le rôle **Propriétaire** pour un compte Data Lake Store sont automatiquement considérées comme des super utilisateurs pour ce compte. Pour en savoir plus sur le contrôle d’accès en fonction du rôle Azure (RBAC), consultez [Contrôle d’accès en fonction du rôle](../active-directory/role-based-access-control-configure.md).

Si vous souhaitez créer un rôle RBAC personnalisé qui dispose des autorisations de super utilisateur. Il doit disposer de ces autorisations :
* Microsoft.DataLakeStore/accounts/Superuser/action
* Microsoft.Authorization/roleAssignments/write

## <a name="the-owning-user"></a>L’utilisateur propriétaire
L’utilisateur qui a créé l’élément est automatiquement l’utilisateur propriétaire de l’élément. Les utilisateurs propriétaires peuvent :

* modifier les autorisations des fichiers détenus ;
* modifier le groupe propriétaire d’un fichier détenu, tant que l’utilisateur propriétaire est également membre du groupe cible.

> [!NOTE]
> L’utilisateur propriétaire **ne peut pas** modifier l’utilisateur propriétaire d’un autre fichier lui appartenant. Seuls les super utilisateurs peuvent modifier l’utilisateur propriétaire d’un fichier ou d’un dossier.
> 
> 

## <a name="the-owning-group"></a>Le groupe propriétaire
Dans les ACL POSIX, chaque utilisateur est associé à un « groupe principal ». Par exemple, l’utilisateur « alice » peut appartenir au groupe « finance ». Alice peut appartenir à plusieurs groupes, mais un groupe est toujours désigné comme son groupe principal. Dans POSIX, lorsqu’Alice crée un fichier, son groupe principal est défini comme groupe propriétaire de ce fichier, dans le cas présent « finance ».

Lorsqu’un nouvel élément de système de fichiers est créé, Data Lake Store affecte une valeur au groupe propriétaire. 

* **Cas 1** : le dossier racine « / ». Ce dossier est créé lors de la création d’un compte Data Lake Store. Dans ce cas, le groupe propriétaire est défini sur l’utilisateur qui a créé le compte.
* **Cas 2** (tous les autres cas) : lorsqu’un nouvel élément est créé, le groupe propriétaire est copié à partir du dossier parent.

Le groupe propriétaire peut être modifié par :

* les super utilisateurs ;
* l’utilisateur propriétaire, si l’utilisateur propriétaire est également membre du groupe cible.

## <a name="access-check-algorithm"></a>Algorithme de vérification des accès
L’illustration suivante représente l’algorithme de vérification des accès pour les comptes Data Lake Store.

![Algorithme d’ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)

## <a name="the-mask-and-effective-permissions"></a>Le masque et les « autorisations effectives »
Le **masque** est une valeur RWX utilisée pour limiter l’accès aux **utilisateurs nommés**, au **groupe propriétaire** et aux **groupes nommés** lors de l’exécution de l’algorithme de vérification des accès. Voici les concepts clés du masque. 

* Le masque crée des « autorisations effectives », c’est-à-dire qu’il modifie les autorisations au moment de la vérification des accès.
* Le masque peut être modifié directement par le propriétaire du fichier et les super utilisateurs.
* Le masque a la possibilité de supprimer des autorisations pour créer l’autorisation effective. Le masque **ne peut pas** ajouter d’autorisations à l’autorisation effective. 

Prenons quelques exemples. Ci-dessous, le masque est défini sur **RWX**, ce qui signifie qu’il ne supprime aucune autorisation. Notez que les autorisations effectives pour un utilisateur nommé, un groupe propriétaire et un groupe nommé ne sont pas modifiées lors de la vérification des accès.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

Dans l’exemple ci-dessous, le masque est défini sur **R-X**. Par conséquent, il **désactive l’autorisation d’écriture** de **l’utilisateur nommé**, du **groupe propriétaire** et du **groupe nommé** au moment de la vérification des accès.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Pour référence, voici où apparaît le masque d’un fichier ou d’un dossier dans le portail Azure.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

> [!NOTE]
> Pour un nouveau compte Data Lake Store, le masque de l’ACL d’accès et de l’ACL par défaut du dossier racine (« / ») est défini par défaut sur RWX.
> 
> 

## <a name="permissions-on-new-files-and-folders"></a>Autorisations sur les nouveaux fichiers et dossiers
Lorsqu’un nouveau fichier ou dossier est créé dans un dossier existant, l’ACL par défaut sur le dossier parent détermine :

* L’ACL par défaut et l’ACL d’accès pour un dossier enfant
* L’ACL d’accès pour un fichier enfant (ces fichiers n’ont pas d’ACL par défaut)

### <a name="a-child-file-or-folders-access-acl"></a>L’ACL d’accès pour un fichier ou dossier enfant
Lorsqu’un fichier ou dossier enfant est créé, l’ACL par défaut du parent est copiée comme ACL d’accès du fichier ou du dossier enfant. En outre, si un **autre** utilisateur dispose des autorisations RWX dans l’ACL par défaut du parent, il est totalement supprimé de l’ACL d’accès de l’élément enfant.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Dans la plupart des cas, les informations ci-dessus constituent tout ce que vous devez savoir sur la manière dont l’ACL d’accès d’un élément enfant est définie. Toutefois, si vous connaissez les systèmes POSIX et que vous souhaitez comprendre dans le détail comment cette transformation est réalisée, consultez la section [Rôle de l’umask lors de la création de l’ACL d’accès pour les nouveaux fichiers et dossiers](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) , plus loin dans cet article.

### <a name="a-child-folders-default-acl"></a>ACL par défaut pour un dossier enfant
Lors de la création d’un dossier enfant sous un dossier parent, l’ACL par défaut du dossier parent est copiée, telle quelle, en tant qu’ACL par défaut du dossier enfant.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Rubriques avancées permettant de comprendre les ACL dans Data Lake Store
Voici quelques rubriques avancées pour vous aider à comprendre comment les ACL sont déterminées pour les fichiers ou dossiers Date Lake Store.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Rôle de l’umask lors de la création de l’ACL d’accès pour les nouveaux fichiers et dossiers
Dans un système compatible POSIX, le concept général est que l’umask est une valeur 9 bits sur le dossier parent permettant de transformer l’autorisation pour **l’utilisateur propriétaire**, le **groupe propriétaire** et les **autres rôles** sur l’ACL d’accès d’un nouveau fichier ou dossier enfant. Les bits d’un umask identifient les bits à désactiver dans l’ACL d’accès de l’élément enfant. Par conséquent, il est utilisé pour empêcher, de manière sélective, la propagation des autorisations pour l’utilisateur propriétaire, le groupe propriétaire et les autres rôles.

Dans un système HDFS, l’umask est généralement une option de configuration à l’échelle du site, contrôlée par les administrateurs. Data Lake Store utilise un **umask à l’échelle du compte** qui ne peut pas être modifié. La table suivante présente l’umask de Data Lake Store.

| Groupe d'utilisateurs | Paramètre | Effet sur l’ACL d’accès du nouvel élément enfant |
| --- | --- | --- |
| utilisateur propriétaire |--- |Aucun effet |
| groupe propriétaire |--- |Aucun effet |
| autre |RWX |Supprimer les autorisations Lecture + Écriture + Exécution |

L’illustration suivante représente ce umask en action. L’effet immédiat est la suppression des autorisations **Lecture + Écriture + Exécution** pour les **autres** utilisateurs. Dans la mesure où l’umask n’a pas spécifié de bits pour **l’utilisateur propriétaire** et le **groupe propriétaire**, ces autorisations ne sont pas modifiées.

![ACL Data Lake Store](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### <a name="the-sticky-bit"></a>Le sticky bit
Le sticky bit est une fonctionnalité avancée d’un système de fichiers POSIX. Dans le contexte de Data Lake Store, il est peu probable que le sticky bit soit nécessaire.

La table ci-dessous montre comment le sticky bit fonctionne dans Data Lake Store.

| Groupe d'utilisateurs | Fichier | Dossier |
| --- | --- | --- |
| Sticky bit **DÉSACTIVÉ** |Aucun effet |Aucun effet |
| Sticky bit **ACTIVÉ** |Aucun effet |Empêche une personne autre que les **super utilisateurs** et **l’utilisateur propriétaire** d’un élément enfant de supprimer ou de renommer cet élément enfant. |

Le sticky bit n’est pas affiché dans le portail Azure.

## <a name="common-questions-for-acls-in-data-lake-store"></a>Questions les plus fréquentes sur les ACL dans Data Lake Store
Voici quelques questions fréquentes concernant les ACL dans Data Lake Store.

### <a name="do-i-have-to-enable-support-for-acls"></a>Dois-je activer la prise en charge des ACL ?
Non. Le contrôle d’accès via les ACL est toujours activé pour les comptes Data Lake Store.

### <a name="what-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Quelles sont les autorisations nécessaires pour supprimer de manière récursive un dossier et son contenu ?
* L’utilisateur doit disposer des autorisations **Écriture + Exécution**sur le dossier parent.
* L’utilisateur doit disposer des autorisations **Lecture + Écriture + Exécution**sur le dossier à supprimer, et tous les dossiers qu’il contient.

> [!NOTE] 
> La suppression des fichiers dans des dossiers ne nécessite pas l’autorisation Écriture sur ces fichiers. En outre, le dossier racine « / » ne peut **jamais** être supprimé.
>
>

### <a name="who-is-set-as-the-owner-of-a-file-or-folder"></a>Qui est défini comme propriétaire d’un fichier ou d’un dossier ?
Le créateur d’un fichier ou d’un dossier en devient le propriétaire.

### <a name="who-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Qui est défini comme groupe propriétaire d’un fichier ou d’un dossier lors de sa création ?
Cette information est copiée à partir du groupe propriétaire du dossier parent sous lequel le nouveau fichier ou dossier est créé.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Je suis l’utilisateur propriétaire d’un fichier, mais je n’ai pas les autorisations RWX dont j’ai besoin. Que faire ?
L’utilisateur propriétaire peut modifier les autorisations du fichier pour s’accorder les autorisations RWX dont il a besoin.

### <a name="when-i-look-at-acls-in-the-azure-portal-i-see-user-names-but-through-apis-i-see-guids-why-is-that"></a>Lorsque j’examine les listes de contrôle d’accès dans le portail Azure, je vois des noms d’utilisateur alors que je vois des GUID via les API. Pourquoi ?
Les entrées dans les listes de contrôle d’accès sont stockées sous forme de GUID correspondant aux utilisateurs dans Azure Active Directory (AAD). Les API retourneront les GUID en l’état. Le portail Azure tente de simplifier l’utilisation des listes de contrôle d’accès en traduisant les GUID en noms conviviaux lorsque cela est possible. 

### <a name="why-do-i-sometimes-see-guids-in-the-acls-when-using-the-portal"></a>Pourquoi est-ce que je vois parfois des GUID dans les listes de contrôle d’accès lors de l’utilisation du portail ?
Un GUID apparaît lorsque l’utilisateur n’existe plus dans AAD. Cela se produit généralement lorsque l’utilisateur a quitté l’entreprise ou que son compte a été supprimé dans AAD.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Data Lake Store prend-il en charge l’héritage des ACL ?
Non.

### <a name="what-is-the-difference-between-mask-and-umask"></a>Quelle est la différence entre le masque et umask ?
| masque | umask |
| --- | --- |
| La propriété **mask** est disponible sur tous les fichiers et dossiers. |**umask** est une propriété du compte Data Lake Store. Ainsi, il existe un seul umask dans Data Lake Store. |
| La propriété mask sur un fichier ou dossier peut être modifiée par l’utilisateur propriétaire ou le groupe propriétaire d’un fichier, ou par un super utilisateur. |La propriété umask ne peut être modifiée par aucun utilisateur, même pas par un super utilisateur. Il s’agit d’une valeur constante non modifiable. |
| La propriété mask est utilisée lors de l’exécution de l’algorithme de vérification des accès pour déterminer si un utilisateur a le droit d’effectuer une opération sur un fichier ou dossier. Le rôle du masque est de créer des « autorisations effectives » au moment de la vérification des accès. |L’umask n’est pas utilisé lors de la vérification des accès. L’umask est utilisé pour déterminer l’ACL d’accès des nouveaux éléments enfants d’un dossier. |
| Le masque est une valeur RWX de 3 bits qui s’applique à l’utilisateur nommé, au groupe nommé et à l’utilisateur propriétaire au moment de la vérification des accès. |L’umask est une valeur de 9 bits qui s’applique à l’utilisateur propriétaire, au groupe propriétaire et aux autres rôles d’un nouvel enfant. |

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Comment en savoir plus sur le modèle de contrôle d’accès POSIX ?
* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)
* [Guide des autorisations HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 
* [Forum aux questions POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)
* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)
* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)
* [ACL POSIX sous Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)
* [ACL à l’aide des listes de contrôle d’accès sous Linux](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Voir aussi
* [Présentation d’Azure Data Lake Store](data-lake-store-overview.md)
* [Prise en main d'Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)




<!--HONumber=Dec16_HO2-->


