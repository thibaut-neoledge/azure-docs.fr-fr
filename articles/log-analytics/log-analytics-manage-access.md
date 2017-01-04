---
title: "Gérer les espaces de travail | Microsoft Docs"
description: "Gérez les espaces de travail dans Log Analytics avec diverses tâches administratives sur les utilisateurs, comptes, espaces de travail et comptes Azure."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/02/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: b78199a672528c475f4f299faaf6406089e95d01


---
# <a name="manage-workspaces"></a>Gestion des espaces de travail

Pour gérer l’accès à Log Analytics, effectuez diverses tâches administratives liées aux espaces de travail. Cet article fournit des conseils pratiques et des procédures que vous utiliserez pour gérer les espaces de travail à l’aide de différents types de comptes. Un espace de travail est en fait un conteneur qui inclut des informations de compte et des informations de configuration simple pour le compte. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Pour créer un espace de travail, vous devez :

1. J’ai un abonnement Azure.
2. Choisissez un nom d’espace de travail.
3. Associez l’espace de travail à votre abonnement.
4. Choisissez un emplacement géographique.

## <a name="determine-the-number-of-workspaces-you-need"></a>Définition du nombre d’espaces de travail nécessaires
Un espace de travail est une ressource Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans le portail Azure.

Il est possible de créer plusieurs espaces de travail pour permettre aux utilisateurs d’accéder à un ou plusieurs d’entre eux. Minimisez le nombre d’espaces de travail pour que vous puissiez interroger et mettre en corrélation le plus de données. Cette section décrit dans quelles conditions il peut être utile de créer plusieurs espaces de travail.

Aujourd'hui, un espace de travail fournit :

* un emplacement géographique pour le stockage des données ;
* des données granulaires pour la facturation ;
* l’isolation des données.

Compte tenu des caractéristiques ci-dessus, vous pouvez créer plusieurs espaces de travail si :

* Vous travaillez pour une entreprise globale et vous avez besoin de stocker vos données dans des régions spécifiques pour des raisons de conformité ou de souveraineté des données.
* Vous utilisez Azure et vous souhaitez éviter les frais liés au transfert de données sortantes en configurant un espace de travail dans la même région que les ressources Azure qu’il gère.
* Vous souhaitez allouer les frais à différents services ou groupes d’entreprise en fonction de leur utilisation. Lorsque vous créez un espace de travail pour chaque service ou groupe d’entreprise, votre relevé de facturation ou d’utilisation Azure indique séparément les frais liés à chaque espace de travail.
* Vous êtes un fournisseur de services gérés et vous devez isoler les données Log Analytics des autres données de vos clients.
* Vous gérez plusieurs clients et vous souhaitez que chaque client/service/groupe d’entreprise ait uniquement accès à ses propres données.

Lorsque vous utilisez des agents pour collecter des données, vous pouvez configurer chacun d’entre eux pour qu’il fournisse des rapports à un ou plusieurs espaces de travail.

Si vous utilisez System Center Operations Manager, chaque groupe d’administration Operations Manager ne peut être connecté qu’à un seul espace de travail. Vous pouvez installer Microsoft Monitoring Agent sur les ordinateurs gérés par Operations Manager et configurer l’agent pour qu’il fournisse des rapports à Operations Manager et à un espace de travail Log Analytics différent.

### <a name="workspace-information"></a>Informations sur l’espace de travail

Vous pouvez afficher des détails sur votre espace de travail dans le portail Azure. Vous pouvez également afficher les détails dans le portail OMS.

#### <a name="view-workspace-information-the-azure-portal"></a>Afficher les informations de l’espace de travail dans le portail Azure

1. Si ce n’est pas déjà fait, connectez-vous au [portail Azure](https://portal.azure.com) à l’aide de votre abonnement Azure.
2. Dans le menu **Hub**, cliquez sur **Plus de services** et, dans la liste des ressources, tapez **Log Analytics**. Au fur et à mesure des caractères saisis, la liste est filtrée. Cliquez sur **Log Analytics**.  
    ![Hub Azure](./media/log-analytics-manage-access/hub.png)  
3. Dans le panneau d’abonnements de Log Analytics, sélectionnez un espace de travail.
4. Le panneau Espace de travail affiche des détails sur l’espace de travail et des liens vers des informations supplémentaires.  
    ![détails sur l’espace de travail](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs
Chaque espace de travail peut être associé à plusieurs comptes utilisateur et chaque compte utilisateur (compte Microsoft ou compte professionnel) peut également avoir accès à plusieurs espaces de travail.

Par défaut, le compte Microsoft ou le compte professionnel utilisé pour créer l’espace de travail devient l’administrateur de l’espace de travail. L’administrateur peut ensuite inviter d’autres comptes Microsoft ou choisir des utilisateurs à partir de Microsoft Azure Active Directory.

Deux emplacements permettent de contrôler l’accès à l’espace de travail :

* Dans Microsoft Azure, le contrôle d’accès basé sur les rôles permet de donner accès à l’abonnement Azure et aux ressources Azure associées. Ces autorisations servent aussi à donner accès à l’API REST et à PowerShell.
* Dans le portail OMS, vous pouvez accéder au portail OMS uniquement, et non à l’abonnement Azure.

Les utilisateurs ne voient pas les données dans les vignettes des solutions Sauvegarde et Site Recovery si vous leur donnez accès au portail OMS mais pas à l’abonnement Azure auquel il est lié.
Pour permettre à tous les utilisateurs de voir les données de ces solutions, vérifiez qu’ils disposent au moins d’un accès **Lecteur** au coffre Sauvegarde et au coffre Site Recovery liés à l’espace de travail.   

### <a name="managing-access-to-log-analytics-using-the-azure-portal"></a>Gestion de l’accès à Log Analytics à l’aide du portail Azure
Si vous octroyez à certaines personnes un accès à l’espace de travail Log Analytics au moyen d’autorisations Azure, dans le portail Azure par exemple, ces mêmes utilisateurs peuvent accéder au portail Log Analytics. Si des utilisateurs se trouvent dans le portail Azure, ils peuvent accéder au portail OMS en cliquant sur la tâche **Portail OMS** lors de l’affichage de la ressource d’espace de travail Log Analytics.

Tenez compte des considérations suivantes concernant le portail Azure :

* Il ne s’agit pas d’un *contrôle d’accès basé sur les rôles*. Si vous disposez d’autorisations d’accès *Lecteur* dans le portail Azure pour l’espace de travail Log Analytics, vous pouvez apporter des modifications à l’aide du portail OMS. Le portail OMS est associé aux concepts suivants : Administrateur, Collaborateur et Utilisateur en lecture seule. Si le compte avec lequel vous êtes connecté figure dans le service Azure Active Directory lié à l’espace de travail, vous avez le rôle d’administrateur dans le portail OMS ; sinon, vous disposez d’un rôle de collaborateur.
* Lorsque vous vous connectez dans le portail OMS à l’aide de http://mms.microsoft.com, par défaut, vous voyez s’afficher la liste **Sélectionner un espace de travail**. Elle contient uniquement les espaces de travail qui ont été ajoutés à l’aide du portail OMS. Pour afficher les espaces de travail auxquels vous pouvez accéder avec un abonnement Azure, vous devez indiquer un locataire dans l’URL. Par exemple :

  `mms.microsoft.com/?tenant=contoso.com` L’identificateur du locataire représente souvent la dernière partie de l’adresse e-mail avec laquelle vous vous connectez.
* Si le compte avec lequel vous êtes connecté est un compte dans le locataire Azure Active Directory, vous bénéficiez d’un rôle *Administrateur* dans le portail OMS. C’est généralement le cas, sauf si vous vous connectez à un fournisseur de services de chiffrement.  Si votre compte ne figure pas dans le répertoire Azure Active Directory du locataire, vous bénéficiez du rôle *Utilisateur* dans le portail OMS.
* Si vous souhaitez accéder directement à un portail auquel vous avez accès par le biais d’autorisations Azure, vous devez spécifier la ressource dans l’URL. Il est possible d’obtenir cette URL à l’aide de PowerShell.

  Par exemple, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  L’URL a l’aspect suivant : `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Gestion des utilisateurs dans le portail OMS
Vous pouvez gérer les utilisateurs et les groupes sur l’onglet **Gérer les utilisateurs** sous l’onglet **Comptes** de la page Paramètres.   

![gérer des utilisateurs](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

#### <a name="add-a-user-to-an-existing-workspace"></a>Ajout d’un utilisateur à un espace de travail existant
Procédez comme suit pour ajouter un utilisateur ou un groupe à un espace de travail.

1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Dans la section **Gérer les utilisateurs**, sélectionnez le type de compte à ajouter : **Compte d’organisation**, **Compte Microsoft** ou **Support Microsoft**.

   * Si vous choisissez Compte Microsoft, saisissez l’adresse électronique de l’utilisateur associé au compte Microsoft.
   * Si vous choisissez Compte d’organisation, saisissez une partie du nom de l’utilisateur ou du groupe ou l’alias de messagerie électronique. Une liste d’utilisateurs et de groupes s’affiche alors dans une zone déroulante. Sélectionnez un utilisateur ou un groupe.
   * Utilisez Support Microsoft pour donner à un ingénieur du support technique Microsoft ou à un autre employé de Microsoft un accès temporaire à votre espace de travail dans le but de faciliter le dépannage.

     > [!NOTE]
     > Pour obtenir de meilleurs résultats, limitez à trois le nombre de groupes Active Directory associés à un seul compte OMS : un pour les administrateurs, un pour les collaborateurs et un pour les utilisateurs en lecture seule. L’utilisation d’un plus grand nombre de groupes peut avoir une incidence sur les performances de Log Analytics.
     >
     >
4. Choisissez le type d’utilisateur ou de groupe à ajouter : **Administrateur**, **Collaborateur**, ou **Utilisateur en lecture seule**.  
5. Cliquez sur **Add**.

   Si vous ajoutez un compte Microsoft, une invitation à joindre l’espace de travail est envoyée à l’adresse électronique que vous avez fournie. Une fois que l’utilisateur a suivi les instructions figurant dans l’invitation à rejoindre OMS, il peut accéder à cet espace de travail.
   Si vous ajoutez un compte d’organisation, l’utilisateur peut accéder à Log Analytics immédiatement.  

#### <a name="edit-an-existing-user-type"></a>Modification d’un type d’utilisateur existant
Vous pouvez modifier le rôle de compte pour un utilisateur associé à votre compte OMS. Vous pouvez choisir parmi les rôles suivants :

* *Administrateur*: peut gérer les utilisateurs, afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs.
* *Collaborateur*: peut afficher toutes les alertes et agir sur celles-ci, ainsi qu’ajouter et supprimer des serveurs.
* *Utilisateur en lecture seule* : les utilisateurs marqués comme étant en lecture seule ne peuvent pas :

  1. Ajouter ou supprimer des solutions. La galerie de solutions est masquée.
  2. Ajouter/modifier/supprimer des mosaïques sur **Mon tableau de bord**.
  3. Afficher les pages **Paramètres**. Les pages sont masquées.
  4. Dans la vue de recherche, la configuration de Power BI, les recherches enregistrées et les tâches relatives aux alertes sont masquées.

#### <a name="to-edit-an-account"></a>Pour modifier un compte
1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Sélectionnez le rôle de l’utilisateur que vous souhaitez modifier.
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

### <a name="remove-a-user-from-a-workspace"></a>Supprimer un utilisateur d’un espace de travail
Procédez comme suit pour supprimer un utilisateur d’un espace de travail. Notez que cela ne ferme pas l’espace de travail. Au lieu de cela, cela supprime l’association entre cet utilisateur et l’espace de travail. Si un utilisateur est associé à plusieurs espaces de travail, il peut encore se connecter à OMS et afficher les autres espaces de travail.

1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.
3. Cliquez sur l’option **Supprimer** située en regard du nom d’utilisateur à supprimer.
4. Cliquez sur **Oui** dans la boîte de dialogue de confirmation.

### <a name="add-a-group-to-an-existing-workspace"></a>Ajout d’un groupe à un espace de travail existant
1. Suivez les étapes 1 à 4 ci-dessus pour ajouter un utilisateur à un espace de travail existant.
2. Sous **Choisir un utilisateur/groupe**, sélectionnez **Groupe**.  
   ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3. Entrez le nom d’affichage ou l’adresse électronique du groupe que vous souhaitez ajouter.
4. Sélectionnez le groupe parmi les résultats, puis cliquez sur **Ajouter**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Liaison d’un espace de travail existant à un abonnement Azure
Tous les espaces de travail créés après le 26 septembre 2016 doivent être liés à un abonnement Azure lors de la création. Vous devez lier les espaces de travail créés avant cette date lors de votre prochaine connexion. Lorsque vous créez l’espace de travail à partir du portail Azure ou que vous liez votre espace de travail à un abonnement Azure, votre répertoire Azure Active Directory est lié en tant que compte d’organisation.

![link Azure subscription](./media/log-analytics-manage-access/required-link.png)

> [!IMPORTANT]
> Pour que vous puissiez lier un espace de travail, votre compte Azure doit déjà y avoir accès.  En d’autres termes, le compte que vous utilisez pour accéder au portail Azure doit être **le même** que celui que vous utilisez pour accéder à votre espace de travail. Si ce n’est pas le cas, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).
>
>

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail OMS
Pour lier un espace de travail à un abonnement Azure dans le portail OMS, l’utilisateur connecté doit disposer d’un compte Azure payant.

1. Dans le portail OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**, puis sur l’onglet **Azure Subscription & Data Plan** (Forfait de données et abonnement Azure).
3. Cliquez sur le forfait de données que vous voulez utiliser.
4. Cliquez sur **Save**.  
   ![forfaits d’abonnement et de données](./media/log-analytics-manage-access/subscription-tab.png)

Votre nouveau forfait de données s’affiche dans le ruban du portail OMS, en haut de votre page web.

![Ruban OMS](./media/log-analytics-manage-access/data-plan-changed.png)

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Pour lier un espace de travail à un abonnement Azure dans le portail Azure
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Recherchez **Log Analytics** et sélectionnez-le.
3. Vous voyez la liste des espaces de travail existants. Cliquez sur **Add**.  
   ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. Sous **Espace de travail OMS**, cliquez sur **Ou liez**.  
   ![lier un élément existant](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Cliquez sur **Configurer les paramètres requis**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Vous voyez la liste des espaces de travail qui ne sont pas encore liés à votre compte Azure. Sélectionnez un espace de travail.  
   ![sélectionner des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Si nécessaire, vous pouvez modifier les valeurs correspondant aux éléments suivants :
   * Abonnement
   * Groupe de ressources
   * Emplacement
   * Niveau tarifaire   
     ![modifier des valeurs](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Cliquez sur **OK**. L’espace de travail est maintenant lié à votre compte Azure.

> [!NOTE]
> Si l’espace de travail que vous souhaitez lier ne s’affiche pas, cela signifie que votre abonnement Azure n’a pas accès à l’espace de travail que vous avez créé sur le site web OMS.  Vous devez accorder l’accès à ce compte à partir du portail OMS. Pour ce faire, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Mise à niveau de l’espace de travail vers un plan payant
Il existe trois types de plan pour les espaces de travail OMS : **Gratuit**, **Autonome** et **OMS**.  Si vous êtes abonné au niveau *Gratuit*, vous pouvez envoyer au maximum 500 Mo de données par jour à Log Analytics.  Si vous dépassez ce volume, vous devez migrer votre espace de travail vers un plan payant pour pouvoir collecter des données au-delà de cette limite. Vous pouvez convertir votre type de plan à tout moment.  Pour en savoir plus sur la tarification d’OMS, consultez la rubrique relative aux [détails de tarification](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Utilisation de droits dans le cadre d’un abonnement OMS
Pour utiliser les droits que vous obtenez à l’achat d’un plan OMS E1, OMS E2 ou Complément OMS pour System Center, choisissez le plan *OMS* d’OMS Log Analytics.

Lorsque vous achetez un abonnement OMS, les droits sont ajoutés à votre Contrat Entreprise. Tout abonnement Azure créé dans le cadre de ce contrat peut faire valoir ces droits. Cela vous permet, par exemple, de disposer de plusieurs espaces de travail qui utilisent le droit issu des abonnements OMS.

Pour vous assurer que l’utilisation d’un espace de travail est appliquée à vos droits à partir de l’abonnement OMS, vous devez :

1. Créer votre espace de travail dans un abonnement Azure qui fait partie du Contrat Entreprise incluant l’abonnement OMS
2. Sélectionner le plan *OMS* pour l’espace de travail

> [!NOTE]
> Si votre espace de travail a été créé avant le 26 septembre 2016 et que votre plan de tarification Log Analytics est *Premium*, cet espace de travail utilise les droits du complément OMS pour System Center. Vous pouvez également utiliser vos droits en migrant au niveau de tarification *OMS*.
>
>

Les droits de l’abonnement OMS ne sont pas visibles dans le portail Azure ou OMS. Vous pouvez les consulter, ainsi que l’utilisation, dans le portail Enterprise Portal.  

Si vous devez modifier l’abonnement Azure auquel votre espace de travail est lié, vous pouvez utiliser l’applet de commande Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) .

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Utilisation de l’engagement Azure d’un Contrat Entreprise
Si vous n’avez pas d’abonnement OMS, vous payez séparément pour chaque composant OMS et l’utilisation apparaît sur votre facture Azure.

Si vous disposez d’un engagement monétaire Azure dans le cadre de l’inscription d’entreprise à laquelle vos abonnements Azure sont liés, toute utilisation de Log Analytics est automatiquement débitée de votre engagement monétaire restant.

Si vous devez modifier l’abonnement Azure auquel l’espace de travail est lié, vous pouvez utiliser l’applet de commande Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx) .  

### <a name="change-a-workspace-to-a-paid-data-plan"></a>Migrer un espace de travail vers un plan de données payant
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Recherchez **Log Analytics** et sélectionnez-le.
3. Vous voyez la liste des espaces de travail existants. Sélectionnez un espace de travail.  
4. Dans le volet Espace de travail sous **Général**, cliquez sur **Niveau tarifaire**.  
5. Sous **Niveau tarifaire**, cliquez sur un plan de données, puis cliquez sur **Sélectionner**.  
    ![select plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Quand vous actualisez l’affichage dans le portail Azure, le **niveau tarifaire** mis à jour s’affiche pour le plan sélectionné.  
    ![plan mis à jour](./media/log-analytics-manage-access/manage-access-change-plan04.png)

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Modifier une organisation Azure Active Directory pour un espace de travail

Vous pouvez modifier l’organisation Azure Active Directory d’un espace de travail. La modification dune organisation Azure Active Directory vous permet d’ajouter des utilisateurs et groupes de ce répertoire vers l’espace de travail.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Pour modifier l’organisation Azure Active Directory d’un espace de travail

1. Dans la page Paramètres du portail OMS, cliquez sur **Comptes**, puis sur l’onglet **Gérer les utilisateurs**.  
2. Consultez les informations sur les comptes d’organisation, puis cliquez sur **Ajouter une organisation**.  
    ![modifier l’organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Entrez les informations d’identité de l’administrateur de votre domaine Azure Active Directory. Ensuite, une confirmation indiquant que votre espace de travail est lié à votre domaine Azure Active Directory s’affiche.  
    ![confirmation d’espace de travail lié](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Supprimer un espace de travail Log Analytics
Lorsque vous supprimez un espace de travail Log Analytics, toutes les données relatives à votre espace de travail sont supprimées du service OMS dans les 30 jours.

Si vous êtes administrateur et que plusieurs utilisateurs sont associés à l’espace de travail, l’association entre les utilisateurs et l’espace de travail est rompue. Si les utilisateurs sont associés à d’autres espaces de travail, ils peuvent continuer à utiliser OMS avec ces autres espaces de travail. Toutefois, s’ils ne sont pas associés à des espaces de travail, ils doivent créer un espace de travail pour utiliser OMS.

### <a name="to-delete-a-workspace"></a>Pour supprimer un espace de travail
1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Recherchez **Log Analytics** et sélectionnez-le.
3. Vous voyez la liste des espaces de travail existants. Sélectionnez l’espace de travail que vous souhaitez supprimer.
4. Dans le panneau Espace de travail, cliquez sur **Supprimer**.  
    ![delete](./media/log-analytics-manage-access/delete-workspace01.png)
5. Dans la boîte de dialogue de confirmation Supprimer espace de travail, cliquez sur **Oui**.

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md) pour ajouter des agents et collecter des données.
* [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
* [Configurez les paramètres de proxy et de pare-feu dans Log Analytics](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu pour que les agents puissent communiquer avec le service Log Analytics.



<!--HONumber=Jan17_HO1-->


