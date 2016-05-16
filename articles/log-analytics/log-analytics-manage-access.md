<properties
	pageTitle="Gestion de l’accès à Log Analytics | Microsoft Azure"
	description="Gérez l’accès à Log Analytics en utilisant diverses tâches administratives sur les utilisateurs, les comptes, les espaces de travail OMS et les comptes Azure."
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Gestion de l’accès à Log Analytics

Pour gérer l’accès à Log Analytics, vous allez utiliser diverses tâches administratives sur les utilisateurs, les comptes, les espaces de travail OMS et les comptes Azure. Pour créer un nouvel espace de travail dans OMS (Operations Management Suite), vous choisissez un nom d’espace de travail et l’associez à votre compte, puis vous choisissez un emplacement géographique. Un espace de travail est en fait un conteneur qui inclut des informations de compte et des informations de configuration simple pour le compte. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail OMS pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

L’article [Prise en main de Log Analytics](log-analytics-get-started.md) vous explique comment procéder rapidement à l’installation et l’exécution. Le reste de l’article décrit de manière détaillée les actions nécessaires pour gérer l’accès à OMS.

Même si, dans un premier temps, vous ne devrez peut-être pas effectuer chaque tâche de gestion, nous aborderons toutes les tâches courantes que vous êtes susceptible d’utiliser dans les sections suivantes :

- Gérer les comptes et les utilisateurs
- Ajout d’un groupe à un espace de travail existant
- Définition du nombre d’espaces de travail nécessaires
- Liaison d’un espace de travail existant à un abonnement Azure
- Mise à niveau d’un espace de travail vers un plan de données payant
- Modification d’un type de plan de données
- Ajout d’une organisation Azure Active Directory à un espace de travail existant
- Contrôle de l’accès aux ressources OMS Log Analytics
- Fermeture de votre espace de travail OMS

## Gérer les comptes et les utilisateurs
Gérez les comptes et les utilisateurs depuis l’onglet **Comptes** sur la page Paramètres. Vous pouvez alors exécuter les tâches des sections suivantes.

![gérer des utilisateurs](./media/log-analytics-manage-access/setup-workspace-manage-users.png)

### Ajout d’un utilisateur à un espace de travail existant

Procédez comme suit pour ajouter un utilisateur ou un groupe à un espace de travail OMS. L’utilisateur ou le groupe sera en mesure d’afficher et d’agir sur toutes les alertes associées à cet espace de travail.

>[AZURE.NOTE] Si vous souhaitez ajouter un utilisateur ou un groupe à partir de votre compte professionnel Azure Active Directory, vous devez vous assurer que vous avez associé votre compte OMS à votre domaine Active Directory. Voir [Ajout d’une organisation Azure Active Directory à un espace de travail existant](#add-an-azure-active-directory-organization-to-an-existing-workspace).

#### Pour ajouter un utilisateur à un espace de travail existant
1. Dans OMS, cliquez sur la vignette **Paramètres**.
2. Cliquez sur l’onglet **Comptes**.
3. Dans la section **Gérer les utilisateurs**, sélectionnez le type de compte à ajouter : **Compte d’organisation** ou **Compte Microsoft**.
    - Si vous choisissez Compte Microsoft, saisissez l’adresse électronique de l’utilisateur associé au compte Microsoft.
    - Si vous choisissez Compte d’organisation, saisissez une partie du nom de l’utilisateur ou du groupe ou l’alias de messagerie électronique. Une liste d’utilisateurs et de groupes s’affiche alors. Sélectionnez un utilisateur ou un groupe.

    >[AZURE.NOTE] Pour de meilleurs résultats, limitez à deux le nombre de groupes Active Directory associés à un seul compte OMS : un pour les administrateurs et un pour les utilisateurs. L’utilisation de plusieurs groupes peut avoir une incidence sur les performances de Log Analytics.

7. Choisissez le type d’utilisateur ou de groupe à ajouter : **Administrateur** ou **Utilisateur**.
8. Cliquez sur **Ajouter**.

  Si vous ajoutez un compte Microsoft, une invitation à joindre l’espace de travail est envoyée à l’adresse électronique que vous avez fournie. Après avoir suivi les instructions pour rejoindre OMS, l’utilisateur peut visualiser les alertes et les informations du compte OMS et vous pourrez afficher les informations de l’utilisateur dans l’onglet **Comptes**, sur la page **Paramètres**. Si vous ajoutez un compte professionnel, l’utilisateur pourra accéder à Log Analytics immédiatement. ![e-mail d’invitation](./media/log-analytics-manage-access/setup-workspace-invitation-email.png)

### Modification d’un type d’utilisateur existant

Vous pouvez modifier le rôle de compte pour un utilisateur associé à votre compte OMS. Vous pouvez choisir parmi les rôles suivants :

 - *Administrateur* : peut gérer les utilisateurs, afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs

 - *Utilisateur* : peut afficher et agir sur toutes les alertes, ainsi qu’ajouter et supprimer des serveurs

#### Pour modifier un compte
1. Sur la page **Paramètres**, dans l’onglet **Comptes** dans OMS, sélectionnez le rôle de l’utilisateur que vous souhaitez modifier.
2. Cliquez sur **OK**.

## Suppression d’un utilisateur d’un espace de travail OMS

Procédez comme suit pour supprimer un utilisateur d’un espace de travail OMS. Notez que cela ne ferme pas l’espace de travail de l’utilisateur. Au lieu de cela, cela supprime l’association entre cet utilisateur et l’espace de travail. Si un utilisateur est associé à plusieurs espaces de travail, il reste en mesure de se connecter à OMS.

### Pour supprimer un utilisateur d’un espace de travail

1. Sur la page **Paramètres**, dans l’onglet **Comptes** d’OMS, cliquez sur Supprimer, en regard du nom d’utilisateur que vous souhaitez supprimer.
2. Cliquez sur **OK** pour confirmer que vous souhaitez supprimer l’utilisateur.


## Ajout d’un groupe à un espace de travail existant

1.	Suivez les étapes 1 à 4 ci-dessus pour ajouter un utilisateur à un espace de travail existant.
2.	Sous **Choisir un utilisateur/groupe**, sélectionnez **Groupe**. ![ajouter un groupe à un espace de travail existant](./media/log-analytics-manage-access/add-group.png)
3.	Entrez le nom d’affichage ou l’adresse électronique du groupe que vous souhaitez ajouter.
4.	Sélectionnez le groupe parmi les résultats, puis cliquez sur **Ajouter**.

## Définition du nombre d’espaces de travail nécessaires

Un espace de travail est considéré comme une ressource Azure dans le portail de gestion Azure.

Vous pouvez créer un espace de travail ou créer un lien vers un espace de travail existant, ouvert précédemment pour une utilisation avec System Center Operations Manager, mais pas encore associé à un abonnement Azure (nécessaire pour la facturation).

Un espace de travail représente le niveau auquel les données sont collectées, agrégées, analysées et présentées dans le portail OMS. Vous pouvez choisir plusieurs espaces de travail pour séparer les données de différents environnements et systèmes ; chaque groupe d’administration Operations Manager (et tous ses agents) ou des machines virtuelles/agents ne peuvent être connectés qu’avec un seul espace de travail.

Chaque espace de travail peut être associé à plusieurs comptes utilisateur et chaque compte utilisateur (compte Microsoft ou compte professionnel) peut également avoir accès à plusieurs espaces de travail OMS.

Par défaut, le compte Microsoft ou le compte professionnel utilisé pour créer l’espace de travail devient l’administrateur de l’espace de travail. L’administrateur peut ensuite inviter d’autres comptes Microsoft ou choisir les utilisateurs à partir de son Azure Active Directory.

## Liaison d’un espace de travail existant à un abonnement Azure

Il est possible de créer un espace de travail à partir du site web [microsoft.com/oms](https://microsoft.com/oms). Toutefois, ces espaces de travail disposent de certaines limites, la plus notable étant celle de 500 Mo de téléchargement de données par jour si vous utilisez un compte gratuit. Pour apporter des modifications à cet espace de travail, vous devez **lier votre espace de travail existant à un abonnement Azure**.

>[AZURE.IMPORTANT] Pour lier un espace de travail, votre compte Azure doit déjà avoir accès à l’espace de travail que vous souhaitez lier. En d’autres termes, le compte que vous utilisez pour accéder au portail Azure doit être **le même** que le compte que vous utilisez pour accéder à votre espace de travail OMS. Si ce n’est pas le cas, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).

1.	Connectez-vous au [portail Azure](http://portal.azure.com).
2.	Recherchez et sélectionnez **Log Analytics (OMS)**.
3.	Vous verrez la liste des espaces de travail existants. Cliquez sur **Ajouter**. ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4.	Sous **Espace de travail OMS**, cliquez sur **Ou lier un élément existant**. ![lier un élément existant](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5.	Cliquez sur **Configurer les paramètres requis**. ![configurer les paramètres requis](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6.	Vous verrez la liste des espaces de travail qui ne sont pas encore liés à votre compte Azure. Sélectionnez un espace de travail. ![sélectionner des espaces de travail](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7.	Si nécessaire, vous pouvez modifier les valeurs correspondant aux éléments suivants :
    - Abonnement
    - Groupe de ressources
    - Emplacement
    - Niveau tarifaire ![modifier des valeurs](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8.	Cliquez sur **Create**. L’espace de travail est maintenant lié à votre compte Azure.

>[AZURE.NOTE] Si l’espace de travail que vous souhaitez lier ne s’affiche pas, cela signifie que votre abonnement Azure n’a pas accès à l’espace de travail OMS que vous avez créé sur le site web OMS. Vous devez accorder l’accès à ce compte à partir de votre espace de travail OMS via le site web OMS. Pour ce faire, consultez [Ajout d’un utilisateur à un espace de travail existant](#add-a-user-to-an-existing-workspace).



## Mise à niveau d’un espace de travail vers un plan de données payant

Il existe trois types de plans de données d’espace de travail pour OMS : **Gratuit**, **Standard** et **Premium**. Si vous utilisez un plan *gratuit*, vous avez peut-être atteint votre plafond de données de 500 Mo. Vous devez mettre à niveau votre espace de travail vers un « **plan avec paiement à l’utilisation** » afin de collecter des données au-delà de cette limite. Vous pouvez convertir votre type de plan à tout moment. Pour plus d’informations sur la tarification OMS, consultez les [détails de tarification](https://www.microsoft.com/fr-FR/server-cloud/operations-management-suite/pricing.aspx).

>[AZURE.IMPORTANT] Les plans d’espace de travail peuvent uniquement être modifiés s’ils sont *liés* à un abonnement Azure. Si vous avez créé votre espace de travail dans Azure ou si vous avez *déjà* lié votre espace de travail, vous pouvez ignorer ce message. Si vous avez créé votre espace de travail à partir du [site web OMS](http://www.microsoft.com/oms), vous devez suivre les étapes de [liaison d’un espace de travail existant à un abonnement Azure](#link-an-existing-workspace-to-an-azure-subscription).

### Utilisation des droits du module complémentaire OMS pour System Center

Le module complémentaire OMS pour System Center donne droit au plan Premium d’OMS Log Analytics, tel que décrit dans [Tarification OMS](https://www.microsoft.com/fr-FR/server-cloud/operations-management-suite/pricing.aspx).

Si vous achetez le module complémentaire OMS pour System Center, votre équipe de compte Microsoft ou votre revendeur associera les modules complémentaires OMS au Contrat Entreprise incluant vos achats Azure. Votre module complémentaire OMS crée un droit dans votre contrat, et n’importe quel abonnement Azure peut faire valoir ce droit. Cela vous permet, par exemple, de disposer de plusieurs espaces de travail OMS qui utilisent le droit du module complémentaire OMS.

Pour vous assurer que l’utilisation d’un espace de travail OMS est appliquée à vos droits à partir du module complémentaire OMS, vous devez :

1. Lier votre espace de travail OMS à un abonnement Azure qui fait partie du Contrat Entreprise incluant à la fois l’achat du module complémentaire OMS et l’utilisation de l’abonnement Azure
2. Sélectionner le plan Premium pour l’espace de travail

Lorsque vous examinez votre utilisation du portail Azure ou OMS, vous ne verrez pas les droits correspondant au module complémentaire OMS. Toutefois, vous pouvez voir les droits sur le portail Enterprise.

Si vous devez modifier l’abonnement Azure auquel votre espace de travail OMS est lié, vous pouvez utiliser l’applet de commande Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx).

### Utilisation de l’engagement Azure d’un Contrat Entreprise

Si vous choisissez d’utiliser une tarification autonome pour les composants OMS, vous payez séparément pour chaque composant OMS et l’utilisation figure sur votre facture Azure.

Si vous avez payé à l’avance pour une certaine quantité d’utilisation d’Azure dans le cadre de votre Contrat Entreprise, l’utilisation d’OMS sera déduite de votre utilisation prépayée. Pour utiliser la tarification de votre engagement Azure pour OMS Log Analytics, l’abonnement auquel est lié l’espace de travail OMS doit faire partie du Contrat Entreprise Azure.

Si vous devez modifier l’abonnement Azure auquel l’espace de travail OMS est lié, vous pouvez utiliser l’applet de commande Azure PowerShell [Move-AzureRMResource](https://msdn.microsoft.com/library/mt652516.aspx).



### Pour transformer un espace de travail vers un plan de données payant

1.	Connectez-vous au [portail Azure](http://portal.azure.com).
2.	Recherchez et sélectionnez **Log Analytics (OMS)**.
3.	Vous verrez la liste des espaces de travail existants. Sélectionnez un espace de travail. ![liste des espaces de travail](./media/log-analytics-manage-access/manage-access-change-plan01.png)
4.	Sous **Paramètres**, cliquez sur **Niveau tarifaire**. ![niveau tarifaire](./media/log-analytics-manage-access/manage-access-change-plan02.png)
5.	Sous **Niveau tarifaire**, sélectionnez un plan de données, puis cliquez sur **Sélectionner**. ![select plan](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6.	Lorsque vous actualisez l’affichage dans le portail Azure, le **niveau tarifaire** mis à jour apparaîtra pour le plan que vous avez sélectionné. ![mettre à jour le niveau tarifaire](./media/log-analytics-manage-access/manage-access-change-plan04.png)

Vous pouvez désormais collecter des données au-delà du plafond de données « gratuit ».


## Ajout d’une organisation Azure Active Directory à un espace de travail existant

Vous pouvez associer votre espace de travail Operational Insights (OMS) à un domaine Azure Active Directory. Vous pouvez ainsi ajouter des utilisateurs à partir d’Active Directory directement à votre espace de travail OMS, sans compte Microsoft distinct.

### Pour ajouter une organisation Azure Active Directory à un espace de travail existant

1. Sur la page Paramètres d’OMS, cliquez sur **Comptes** puis sur **Informations sur l’espace de travail**.  
2. Consultez les informations sur les comptes d’organisation, puis cliquez sur **Ajouter une organisation**. ![ajouter une organisation](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Entrez les informations d’identité de l’administrateur de votre domaine Azure Active Directory. Vous verrez ensuite une confirmation indiquant que votre espace de travail est lié à votre domaine Azure Active Directory. ![confirmation d’espace de travail lié](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## Contrôle de l’accès aux ressources OMS Log Analytics

Deux emplacements permettent de contrôler l’accès à l’espace de travail OMS :

- L’accès au portail OMS est géré au sein du portail OMS, séparément de l’accès aux abonnements Azure incluant les ressources.
- L’accès à PowerShell et l’accès direct à l’API REST sont gérés dans Azure à l’aide d’Azure RBAC.

Si vous avez autorisé certaines personnes à accéder au portail OMS mais pas à l’abonnement Azure auquel il est lié, les vignettes des solutions Automation, Backup et Site Recovery n’afficheront aucune donnée lorsque l’utilisateur se connecte au portail OMS.

Pour permettre à tous les utilisateurs de voir les données de ces solutions, assurez-vous qu’ils ont au moins un accès en **lecture** pour le compte Automation, le coffre de sauvegarde et le coffre Site Recovery liés à l’espace de travail OMS.


## Fermeture de votre espace de travail OMS

Lorsque vous fermez un espace de travail OMS, toutes les données relatives à votre espace de travail sont supprimées du service OMS dans les 30 jours suivant la fermeture de l’espace de travail.

Si vous êtes administrateur et que plusieurs utilisateurs sont associés à l’espace de travail, l’association entre les utilisateurs et l’espace de travail est rompue. Si les utilisateurs sont associés à d’autres espaces de travail, ils peuvent continuer à utiliser OMS avec ces autres espaces de travail. Toutefois, s’ils ne sont pas associés à des espaces de travail, ils devront créer un espace de travail pour utiliser OMS.

### Pour fermer un espace de travail OMS

1. Sur la page **Paramètres**, dans l’onglet **Comptes** d’OMS, cliquez sur **Fermer l’espace de travail**.

2. Sélectionnez un des motifs de fermeture de votre espace de travail ou saisissez une autre raison dans la zone de texte.

3. Cliquez sur **Fermer l’espace de travail**.

## Étapes suivantes

- Consultez la page [Connecter des ordinateurs Windows à Log Analytics](log-analytics-windows-agents.md) pour ajouter des agents et collecter des données.
- [Ajoutez des solutions Log Analytics à partir de la galerie de solutions](log-analytics-add-solutions.md) pour ajouter des fonctionnalités et collecter des données.
- [Configurez les paramètres de proxy et de pare-feu dans Log Analytics](log-analytics-proxy-firewall.md) si votre organisation utilise un serveur proxy ou un pare-feu pour que les agents puissent communiquer avec le service Log Analytics.

<!---HONumber=AcomDC_0504_2016-->