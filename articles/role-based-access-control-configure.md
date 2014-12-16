<properties title="Role Based Access Control in Azure Preview Portal" pageTitle="Contrôle d'accès en fonction du rôle dans la version préliminaire du portail Azure" description="Describes how role based access control works and how to set it up" metaKeywords="" services="multiple" solutions="" documentationCenter="" authors="justinha" videoId="" scriptId="" manager="terrylan" />

<tags ms.service="multiple" ms.devlang="dotnet" ms.topic="article" ms.tgt_pltfrm="Ibiza" ms.workload="infrastructure-services" ms.date="09/12/2014" ms.author="justinha;Justinha@microsoft.com" />

<!--This is a basic template that shows you how to use mark down to create a topic that includes a TOC, sections with subheadings, links to other azure.microsoft.com topics, links to other sites, bold text, italic text, numbered and bulleted lists, code snippets, and images. For fancier markdown, find a published topic and copy the markdown or HTML you want. For more details about using markdown, see http://sharepoint/sites/azurecontentguidance/wiki/Pages/Content%20Guidance%20Wiki%20Home.aspx.-->

<!--Properties section (above): this is required in all topics. Please fill it out!-->

<!--The next line, with one pound sign at the beginning, is the page title--> 
# Contrôle d'accès basé sur un rôle dans la version préliminaire du portail Azure 

<p> La version préliminaire du portail de gestion Azure prend désormais en charge le contrôle d'accès en fonction du rôle afin de permettre aux organisations de respecter purement et simplement les conditions requises en matière de gestion d'accès. Le <a href="http://go.microsoft.com/fwlink/?LinkId=511576" target="_blank">billet de blog</a> présente brièvement la fonctionnalité et vous permet de la prendre en main. Cette rubrique décrit en détail les différents concepts et aborde divers cas d'utilisation supplémentaires. 

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

##Sommaire##

* [RBAC dans Azure](#whatisrbac) 
* [Coexistence du RBAC et des coadministrateurs d'abonnement](#coexist)
* [Autorisation pour les opérations de gestion et les opérations de données](#authmgmt)
* [Ajout et suppression de l'accès](#addremoveaccess)
* [Problèmes connus lors de l'utilisation du contrôle d'accès basé sur un rôle](#knownissues)
* [Commentaires](#feedback)
* [Étapes suivantes](#next)

<h2><a id="whatisrbac"></a>RBAC dans Azure</h2>
                                                                   
Chaque abonnement Azure est associé à un annuaire Azure Active Directory. Les utilisateurs et services qui accèdent aux ressources de l'abonnement en utilisant les API du portail de gestion Azure ou du gestionnaire de ressources Azure doivent s'authentifier avec cet Azure Active Directory.

![][1] 

Le contrôle d'accès en fonction du rôle Azure vous permet d'accorder l'accès approprié aux utilisateurs, groupes et services Azure AD en leur affectant des rôles sur un abonnement, un groupe de ressources ou un niveau de ressources individuel. Le rôle affecté définit le niveau d'accès des utilisateurs, groupes ou services aux ressources Azure. 

### Rôle

Un rôle est un ensemble d'actions qui peuvent être appliquées à des ressources Azure. Un utilisateur ou un service est autorisé à effectuer une action sur une ressource Azure s'il s'est vu affecter un rôle qui inclut cette action.

#### Rôles intégrés

Dans la version préliminaire, le contrôle d'accès basé sur un rôle inclut trois rôles intégrés qui peuvent être affectés aux utilisateurs, groupes et services.

+ **Propriétaire** : dispose d'un contrôle total des ressources Azure. Le propriétaire peut effectuer toutes les opérations de gestion sur une ressource, y compris la gestion de l'accès. 
+ **Collaborateur** : peut effectuer toutes les opérations de gestion, à l'exception de la gestion de l'accès. Il ne peut donc pas accorder d'accès à d'autres utilisateurs.
+ **Lecteur** : peut uniquement afficher les ressources. Le lecteur n'a pas accès aux informations secrètes associées à une ressource. 

Vous ne pouvez pas modifier la définition des rôles intégrés. Dans une version ultérieure d'Azure RBAC, vous pourrez définir des rôles personnalisés en composant un ensemble d'actions à partir d'une liste des actions applicables aux ressources Azure.

#### Actions et non-actions

La propriété **actions** de la définition du rôle spécifie les actions autorisées sur les ressources Azure. Les chaînes d'action peuvent utiliser des caractères génériques. La propriété **non-actions** de la définition du rôle spécifie les actions qui doivent être exclues des actions autorisées. 

**Rôle intégré**  | Actions  | Pas d'actions	
------------- | -------------  | ------------
Propriétaire (autorise toutes les actions)  | *  | 
Collaborateur (autorise toutes les actions, à l'exception des affectations de rôles d'écriture et de suppression)  | *  | Microsoft.Authorization/ * /Write, Microsoft.Authorization/ * /Delete
Lecteur (autorise toutes les actions de lecture)  | */Read  | 

### Affectation de rôle

L'accès est accordé aux utilisateurs et services Azure AD en leur affectant le rôle approprié sur une ressource Azure. 

#### Entités de sécurité Azure AD

Les rôles peuvent être affectés aux types suivants d'entités de sécurité Azure AD :

+ **Utilisateurs** : les rôles peuvent être affectés aux utilisateurs d'organisation inclus dans le Azure AD associé à l'abonnement Azure. Les rôles peuvent être affectés à des utilisateurs de compte Microsoft externes (par ex., joe@outlook.com) en utilisant l'action d'invitation afin de leur affecter un rôle dans la version préliminaire du portail Azure. Affecter un rôle à un utilisateur de compte Microsoft externe génère un compte invité dans le Azure AD associé. Si le compte invité est désactivé dans l'annuaire, l'utilisateur externe ne pourra accéder à aucune ressource Azure pour laquelle il a reçu un droit d'accès.
+ **Groupes** : Les rôles peuvent être affectés à des groupes de sécurité Azure AD. Un utilisateur se voit automatiquement accorder le droit d'accès à une ressource s'il devient membre d'un groupe qui dispose de cet accès. L'utilisateur perd également automatiquement son droit d'accès à la ressource lorsqu'il est supprimé du groupe. Plutôt que d'affecter des rôles directement aux utilisateurs, la méthode la plus efficace consiste à gérer l'accès via des groupes, en leur affectant des rôles et en leur ajoutant des utilisateurs. Azure RBAC n'autorise pas l'affectation de rôles à des listes de distribution.
	La capacité à affecter des rôles à des groupes permet à une organisation d'étendre au cloud son modèle de contrôle d'accès existant, associé à son annuaire local ; les groupes de sécurité déjà établis pour contrôler l'accès local peuvent ainsi être réutilisés pour contrôler l'accès aux ressources dans la version préliminaire du portail Azure. Pour plus d'informations concernant les différentes options de synchronisation des utilisateurs et groupes à partir d'un annuaire local, voir la section [Intégration d'annuaire](http://technet.microsoft.com/library/jj573653.aspx). Azure AD Premium inclut également une [fonctionnalité de gestion de groupes déléguée](http://msdn.microsoft.com/library/azure/dn641267.aspx), qui permet de déléguer la création et la gestion des groupes à des utilisateurs non-administrateurs à partir de Azure AD.
+ **Principaux du service** : les identités de service sont représentées sous forme de principaux du service dans l'annuaire. Ils authentifient Azure AD et communiquent de façon sécurisée les uns avec les autres. Un service peut se voir accorder l'accès aux ressources Azure en affectant des rôles via le module Azure pour Windows PowerShell au principal du service Azure AD qui représente ce service. 

#### Étendue des ressources

Il n'est pas nécessaire d'accorder l'accès à l'ensemble de l'abonnement. Les rôles peuvent également être affectés à des groupes de ressources et à des ressources individuelles. Dans Azure RBAC, une ressource hérite des affectations de rôles de ses ressources parentes. Par conséquent, lorsqu'un utilisateur, un groupe ou un service se voit accorder l'accès uniquement à un groupe de ressources dans un abonnement, il ne peut accéder qu'à ce seul groupe de ressources et aux ressources incluses, mais à aucun autre groupe de ressources inclus dans l'abonnement. Autre exemple : un groupe de sécurité peut être ajouté au rôle Lecteur d'un groupe de ressources, mais sera ajouté au rôle Collaborateur pour une base de données dans ce groupe de ressources.

![][2]

<h2><a id="coexist"></a>Coexistence du RBAC et des coadministrateurs d'abonnement</h2>

L'administrateur et les coadministrateurs d'abonnement continueront à disposer d'un accès complet aux portails et aux API de gestion Azure. Dans le modèle RBAC, ils se voient affecter le rôle de propriétaire au niveau de l'abonnement.  
Toutefois, le nouveau modèle RBAC est pris en charge uniquement par la version préliminaire du portail Azure et l'API du gestionnaire de ressources Azure. Les utilisateurs et services qui se voient affecter des rôles RBAC ne peuvent pas accéder aux API du portail de gestion Azure ni aux API de gestion des services. Ajouter un utilisateur au rôle Propriétaire d'un abonnement dans la version préliminaire du portail Azure ne fait pas de cet utilisateur un coadministrateur de l'abonnement dans la version complète du portail Azure.

Si vous souhaitez accorder à un utilisateur l'accès à une ressource Azure qui ne peut pas encore être gérée via la version préliminaire du portail Azure, vous devez l'ajouter aux coadministrateurs de l'abonnement en utilisant la version complète du portail de gestion Azure. Service Bus et Cloud Services sont des exemples de ressources qui ne peuvent actuellement pas être gérées à l'aide du RBAC.

<h2><a id="authmgmt"></a>Autorisation pour les opérations de gestion et les opérations de données</h2>

Le contrôle d'accès en fonction du rôle est pris en charge uniquement pour les opérations de gestion des ressources Azure dans les API de la version préliminaire du portail Azure et du gestionnaire de ressources Azure. Toutes les opérations de niveau de données pour les ressources Azure ne peuvent pas être autorisées via le RBAC. Par exemple, les opérations create/read/update/delete des comptes de stockage peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des objets blob ou des tables dans le compte de stockage n'est pas encore possible. De même, les opérations create/read/update/delete d'une base de données SQL peuvent être contrôlées via le RBAC, mais le contrôle des opérations create/read/update/delete des tables SQL dans la base de données n'est pas encore possible.

<h2><a id="addremoveaccess"></a>Ajout et suppression de l'accès</h2>

Examinons un exemple de gestion de l'accès par un propriétaire des ressources au sein d'une organisation. Dans ce scénario, plusieurs personnes travaillent sur divers projets de tests et de production qui sont développés à l'aide des ressources Azure. La méthode la plus efficace pour accorder l'accès doit être adoptée. Les utilisateurs doivent pouvoir accéder à toutes les ressources dont ils ont besoin, mais à ces ressources uniquement. Vous souhaitez réutiliser tous les processus et outils déjà investis dans l'utilisation des groupes de sécurité, qui sont contrôlés depuis un Active Directory local. Les sections suivantes expliquent comment configurer l'accès à ces ressources :

* [Ajout d'accès](#add)
* [Suppression d'accès](#remove)
* [Ajout ou suppression d'accès pour un utilisateur externe](#addremoveext)

<h3><a id="add"></a>Ajout d'accès</h2>

Voici un récapitulatif des spécifications d'accès dans Azure et leur configuration.

Utilisateur/Groupe | Spécification de l'accès | rôle et portée de l'accès	
------------- | -------------  | ------------
Toute l'équipe de Jill Santos  | Lire toutes les ressources Azure  | Ajout du groupe AD qui représente l'équipe de Jill Santos au rôle Lecteur de l'abonnement Azure
Toute l'équipe de Jill Santos  | Créer et gérer toutes les ressources du groupe de ressources Test  | Ajout du groupe AD qui représente l'équipe de Jill Santos au rôle Collaborateur du groupe de ressources Test
Brock | Créer et gérer toutes les ressources dans le groupe de ressources Prod | Ajout de Brock au rôle Collaborateur pour le groupe de ressources Prod


Commençons par ajouter l'accès en Lecture pour toutes les ressources de l'abonnement. Cliquez sur **Parcourir > Tout > Abonnements**.

![][3] 

Cliquez sur le *nom de l'abonnement* ** > Lecteur > Ajouter**. Dans la liste des utilisateurs et des groupes, sélectionnez ou entrez le nom du groupe Active Directory.

![][4]

Ajoutez ensuite la même équipe au rôle Collaborateur du groupe de ressources Test. Cliquez sur le groupe de ressources pour ouvrir son volet de propriétés. Sous **Rôles**, cliquez sur **Collaborateur > Ajouter** et entrez le nom de l'équipe.

![][5]

Pour ajouter Brock au rôle Collaborateur du groupe de ressources Prod, cliquez sur le groupe de ressources, puis sur **Collaborateur > Ajouter**, et entrez le nom de Brock. 

![][6]

Les affectations de rôles peuvent également être gérées en utilisant le module Microsoft Azure pour Windows PowerShell. Voici un exemple qui montre comment ajouter le compte de Brock en utilisant la cmdlet New-AzureRoleAssignment au lieu du portail :

	PS C:\> New-AzureRoleAssignment -Mail brockh@contoso.com -RoleDefinitionName Contributor -ResourceGroupName ProdDB

Pour plus d'informations sur l'utilisation de Windows PowerShell pour ajouter ou supprimer un accès, consultez la section [Gestion du contrôle d'accès en fonction du rôle à l'aide de Windows PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-powershell/). 

<h3><a id="remove"></a>Suppression d'accès</h2>

Il est également facile de supprimer des affectations. Imaginons que vous souhaitez supprimer l'utilisateur Brad Adams du rôle Lecteur d'un groupe de ressources nommé TestDB. Ouvrez le volet du groupe de ressources, cliquez sur **Lecteur > Brad Adams > Supprimer**.

![][7]

Voici un exemple qui montre comment supprimer Brad Adams en utilisant la cmdlet Remove-AzureRoleAssignment :

	PS C:\> Remove-AzureRoleAssignment -Mail badams@contoso.com -RoleDefinitionName Reader -ResourceGroupName TestDB

<h3><a id="addremoveext"></a>Ajout ou suppression d'accès pour un utilisateur externe</h2>

L'onglet **Configurer** d'un annuaire inclut des options de contrôle d'accès des utilisateurs externes. Ces options peuvent être modifiées uniquement dans l'interface utilisateur (il n'existe aucune méthode Windows PowerShell ou API) dans le portail Azure complet par un administrateur global de l'annuaire. 
Pour ouvrir l'onglet **Configurer** dans la version complète du portail Azure, cliquez sur **Active Directory**, puis sur le nom de l'annuaire.

![][10]

Vous pouvez ensuite modifier les options de contrôle d'accès des utilisateurs externes. 

![][8]

Par défaut, les invités ne peuvent pas énumérer les éléments de contenu de l'annuaire ; ils ne peuvent donc pas afficher les utilisateurs ou groupes dans la **Liste des membres**. Ils peuvent rechercher un utilisateur en entrant son adresse e-mail complète, puis accorder l'accès. Voici l'ensemble des restrictions par défaut pour les invités :

- Ils ne peuvent pas énumérer les utilisateurs ou groupes dans l'annuaire.
- Ils ne peuvent accéder qu'à certaines informations limitées d'un utilisateur s'ils ne connaissent pas son adresse e-mail.
- Ils peuvent accéder à certaines informations limitées d'un groupe s'ils connaissent son nom.

La capacité des invités à n'accéder qu'à certaines informations limitées d'un utilisateur ou d'un groupe les autorise à inviter d'autres personnes et afficher certains détails des personnes avec qui ils collaborent.  

Voyons comment le processus d'ajout d'accès fonctionne pour un utilisateur externe. Nous allons ajouter un utilisateur externe au même rôle Lecteur que pour le groupe de ressources TestDB afin de lui permettre de déboguer une erreur. Ouvrez le volet du groupe de ressources, cliquez sur **Lecteur > Ajouter > Inviter** et entrez l'adresse de messagerie de l'utilisateur à ajouter. 

![][9]

Lorsque vous ajoutez un utilisateur externe, un invité est créé dans l'annuaire. Cet invité peut ensuite être ajouté à un groupe ou supprimé d'un groupe, ou vous pouvez l'ajouter à un rôle ou le supprimer d'un rôle individuellement, comme vous le feriez pour tout autre utilisateur de l'annuaire. 

Vous pouvez également supprimer un invité de n'importe quel rôle, comme vous le feriez pour n'importe quel utilisateur. Supprimer un invité d'un rôle sur une ressource ne le supprime pas de l'annuaire. 
 
<h2><a id="knownissues"></a>Problèmes connus lors de l'utilisation du contrôle d'accès basé sur un rôle</h2>

En cas de problème lors de l'utilisation de la fonctionnalité de contrôle d'accès en fonction du rôle dans la version préliminaire, consultez la section [Résolution des problèmes de contrôle d'accès en fonction du rôle](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-troubleshooting/), qui inclut tous les problèmes connus et susceptibles de vous intéresser.


<h2><a id="feedback"></a>Commentaires</h2>

Essayez Azure RBAC et envoyez-nous vos [feedback](http://aka.ms/azurerbacfeedback). 


<h2><a id="next"></a>Étapes suivantes</h2>

Voici quelques ressources supplémentaires destinées à faciliter votre utilisation du contrôle d'accès en fonction du rôle : 

+ [Gestion du contrôle d'accès basé sur un rôle à l'aide de Windows PowerShell](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-powershell/)
+ [Gestion du contrôle d'accès basé sur un rôle à l'aide de l'interface de ligne de commande XPLAT](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-xplat-cli/)
+ [Résolution des problèmes de contrôle d'accès basé sur un rôle](http://azure.microsoft.com/fr-fr/documentation/articles/role-based-access-control-troubleshooting/)
+ [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx)
+ [Azure Active Directory Premium et Basic](http://msdn.microsoft.com/fr-fr/library/azure/dn532272.aspx)
+ [Association des abonnements Azure avec Azure AD](http://msdn.microsoft.com/fr-fr/library/azure/dn629581.aspx)
+ Pour une présentation de la gestion de groupe libre-service pour les groupes de sécurité, consultez le [Blog de l'équipe Active Directory](http://blogs.technet.com/b/ad/archive/2014/02/24/more-preview-enhancements-for-windows-azure-ad-premium.aspx)



<!--Image references-->
[1]: ./media/role-based-access-control-configure/RBACSubAuthDir.png
[2]: ./media/role-based-access-control-configure/RBACAssignmentScopes.png
[3]: ./media/role-based-access-control-configure/RBACSubscriptionBlade.png
[4]: ./media/role-based-access-control-configure/RBACAddSubReader_NEW.png
[5]: ./media/role-based-access-control-configure/RBACAddRGContributor_NEW.png
[6]: ./media/role-based-access-control-configure/RBACAddProdContributor_NEW.png
[7]: ./media/role-based-access-control-configure/RBACRemoveRole.png
[8]: ./media/role-based-access-control-configure/RBACGuestAccessControls.png
[9]: ./media/role-based-access-control-configure/RBACInviteExtUser_NEW.png
[10]: ./media/role-based-access-control-configure/RBACDirConfigTab.png


