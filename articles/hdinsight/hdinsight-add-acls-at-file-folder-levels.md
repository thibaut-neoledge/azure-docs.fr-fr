---
title: "Gérer les autorisations des utilisateurs au niveau des fichiers et des dossiers - Azure HDInsight | Microsoft Docs"
description: "Guide pratique pour gérer les autorisations de fichier et de dossier des clusters HDInsight joints à un domaine."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: 9ca91721e691eca239478c4ac8b85e2652babdfd
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="manage-user-permissions-at-the-file-and-folder-levels"></a>Gérer les autorisations des utilisateurs au niveau des fichiers et des dossiers

Les [clusters HDInsight joint à un domaine](./domain-joined/apache-domain-joined-introduction.md) utilisent l’authentification forte avec les utilisateurs d’Azure Active Directory (Azure AD), ainsi que des stratégies de *contrôle d’accès en fonction du rôle* (RBAC) pour différents services, notamment YARN et Hive. Si le magasin de données par défaut de votre cluster est le Stockage Azure, ou des objets Windows Azure Storage Blob (WASB), vous pouvez également appliquer des autorisations au niveau des fichiers et des dossiers. Vous pouvez utiliser Apache Ranger pour contrôler l’accès aux fichiers du cluster chez vos groupes et utilisateurs Azure AD synchronisés.
<!-- [synchronized Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md). -->

L’instance Apache Ranger des clusters joints à un domaine HDInsight est préconfigurée avec le service Ranger-WASB. Le service Ranger-WASB est un moteur de gestion des stratégies similaire à Ranger-HDFS, mais avec une mise en œuvre différente des stratégies d’accès de Ranger. Dans le service Ranger-WASB, si aucune stratégie Ranger ne correspond à une demande de ressource entrante, la réponse par défaut est REFUSER. Le service Ranger ne transmet pas de vérification des autorisations à WASB.

## <a name="permission-and-policy-model"></a>Modèle d’autorisation et de stratégie

Les demandes d’accès aux ressources sont évaluées suivant ce flux :

![Flux d’évaluation des stratégies Apache Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-policy-evaluation-flow.png)

Les règles REFUSER sont évaluées en premier, suivies des règles AUTORISER. À la fin de la mise en correspondance, si aucune stratégie ne concorde, une instruction REFUSER est retournée.

### <a name="user-variable"></a>Variable USER

Vous pouvez utiliser la variable `{USER}` lorsque vous affectez des stratégies permettant à un utilisateur d’accéder à un sous-répertoire `/{username}`, par exemple :

```
resource: path=/app-logs/{USER}, user: {USER}, recursive=true, permissions: all, delegateAdmin=true
```

La stratégie ci-dessus permet aux utilisateurs d’accéder à leur propre sous-dossier dans le répertoire `/app-logs/`. Voici la stratégie correspondante dans l’interface utilisateur Ranger :

![Exemple d’utilisation de la variable USER](./media/hdinsight-add-acls-at-file-folder-levels/user-variable.png)

### <a name="policy-model-examples"></a>Exemples du modèle de stratégie

Le tableau suivant liste différents exemples de fonctionnement du modèle de stratégie :

| Stratégie Ranger | Système de fichiers existant | Demande de l’utilisateur | Résultat |
| -- | -- | -- | -- |
| /data/finance/, bob, ÉCRITURE | /data | bob, Créer un fichier /data/finance/mydatafile.txt | AUTORISER - Création du dossier intermédiaire « finance », en raison de la vérification des ancêtres |
| /data/finance/, bob, ÉCRITURE | /data | alice, Créer un fichier /data/finance/mydatafile.txt | REFUSER - Pas de stratégie correspondante |
| /data/finance*, bob, ÉCRITURE | /data | bob, Créer un fichier /data/finance/mydatafile.txt | AUTORISER - Dans ce cas, présence de la stratégie récursive facultative (`*`) ; consultez la section [Caractères génériques](#wildcards) |
| /data/finance/mydatafile.txt, bob, ÉCRITURE | /data | bob, Créer un fichier /data/finance/mydatafile.txt | REFUSER - Échec de la vérification des ancêtres sur « /data » en raison de l’absence de stratégie |
| /data/finance/mydatafile.txt, bob, ÉCRITURE | /data/finance | bob, Créer un fichier /data/finance/mydatafile.txt | REFUSER - Absence de stratégie pour la vérification des ancêtres sur « /data/finance » |

Les autorisations sont requises au niveau des dossiers ou au niveau des fichiers, en fonction du type d’opération. Par exemple, un appel « lecture/ouverture » nécessite un accès en lecture au niveau du fichier, tandis qu’un appel « création » requiert des autorisations au niveau du dossier ancêtre.

### <a name="wildcards-"></a>Caractères génériques (*)

Quand un caractère générique (`*`) se trouve dans le chemin d’accès d’une stratégie, il s’applique à ce chemin d’accès et à la totalité de sa sous-arborescence. Cette récursivité revient à utiliser un `recurse-flag`. Dans Ranger-WASB, le caractère générique indique à la fois la récursivité et la correspondance partielle de noms.

## <a name="manage-file-and-folder-level-permissions-with-apache-ranger"></a>Gérer les autorisations au niveau des fichiers et des dossiers avec Apache Ranger

Si ce n’est pas encore fait, suivez [ces instructions](./domain-joined/apache-domain-joined-configure.md) pour configurer un nouveau cluster joint à un domaine.

Ouvrez Ranger-WASB en accédant à `https://<YOUR CLUSTER NAME>.azurehdinsight.net/ranger/`. Entrez le nom d’utilisateur de l’administrateur du cluster et le mot de passe que vous avez défini lors de la création de votre cluster.

Une fois connecté, vous pouvez voir le tableau de bord Ranger :

![Tableau de bord Ranger](./media/hdinsight-add-acls-at-file-folder-levels/ranger-dashboard.png)

Pour afficher les autorisations de fichier et de dossier actuelles du compte de stockage Azure associé à votre cluster, cliquez sur le lien ***NOM_CLUSTER*_wasb** dans la zone de contrôle WASB.

![Tableau de bord Ranger](./media/hdinsight-add-acls-at-file-folder-levels/wasb-dashboard-link.png)

La liste des stratégies actuelles s’affiche. Plusieurs stratégies courantes sont incluses afin de servir de point de départ : consultez les détails de chacune pour afficher des exemples d’utilisation.

Pour chaque stratégie, vous pouvez voir si elle est activée et si l’enregistrement d’audit est configuré. Vous pouvez également consulter les groupes et utilisateurs affectés. Chacune possède deux boutons d’action : Modifier et Supprimer.

![Liste des stratégies](./media/hdinsight-add-acls-at-file-folder-levels/policy-list.png)

### <a name="adding-a-new-policy"></a>Ajouter une nouvelle stratégie

1. En haut à droite de la page des stratégies WASB, sélectionnez **Ajouter une nouvelle stratégie**.

    ![Ajouter une nouvelle stratégie](./media/hdinsight-add-acls-at-file-folder-levels/add-new.png)

2. Entrez un **Nom de stratégie** explicite. Spécifiez le **compte de stockage** Azure de votre cluster (*NOM_COMPTE*.blob.core.windows.net), et le **Conteneur du compte de stockage** spécifié lors de la création de votre cluster. Entrez le **Chemin d’accès relatif** (au cluster) du fichier ou du dossier concerné.

    ![Formulaire Nouvelle stratégie](./media/hdinsight-add-acls-at-file-folder-levels/new-policy.png)

3. Sous le formulaire, spécifiez vos **Conditions d’autorisation** pour cette nouvelle ressource. Sélectionnez les utilisateurs et les groupes applicables, et définissez leurs autorisations. Dans l’exemple suivant, nous accordons l’accès en lecture/écriture à tous les utilisateurs du groupe `sales`.

    ![Autoriser les commerciaux](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales.png)

4. Sélectionnez **Enregistrer**.

### <a name="example-policy-conditions"></a>Exemples de conditions de stratégie

Le [flux d’évaluation des stratégies](#permission-and-policy-model) Apache Ranger permet de spécifier toutes les combinaisons possibles de conditions d’autorisation et de refus en fonction des besoins. Voici quelques exemples :

1. Autoriser tous les utilisateurs de l’équipe commerciale, mais aucun stagiaire :

    ![Autoriser les commerciaux, refuser les stagiaires](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns.png)

2. Autoriser tous les utilisateurs de l’équipe commerciale et refuser tous les stagiaires, à l’exception d’un stagiaire nommé « hiveuser3 », qui doit avoir un accès en lecture :

    ![Autoriser les ventes, refuser les stagiaires à l’exception de hiveuser3](./media/hdinsight-add-acls-at-file-folder-levels/allow-sales-deny-interns-except-hiveuser3.png)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer les stratégies Hive dans HDInsight joint à un domaine](./domain-joined/apache-domain-joined-run-hive.md)
* [Gérer des clusters HDInsight joints à un domaine](./domain-joined/apache-domain-joined-manage.md)
* [Gérer Ambari - Autoriser les utilisateurs sur Ambari](hdinsight-authorize-users-to-ambari.md)

<!-- * [Synchronize Azure AD users and groups](hdinsight-sync-aad-users-to-cluster.md) -->

