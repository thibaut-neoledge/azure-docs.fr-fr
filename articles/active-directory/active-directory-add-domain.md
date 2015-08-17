<properties 
	pageTitle="Ajout de votre propre domaine à Azure AD" 
	description="Cette rubrique explique comment ajouter votre propre nom de domaine à Azure AD et comporte des informations connexes." 
	services="active-directory" 
	documentationCenter="" 
	authors="Justinha" 
	manager="TerryLan" 
	editor="LisaToft"/>


<tags 
	ms.service="active-directory" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/12/2015" 
	ms.author="Justinha"/>


# Ajout de votre propre domaine à Azure AD

Quand vous vous inscrivez à un service cloud Microsoft, vous bénéficiez d’un nom de domaine au format suivant : contoso.onmicrosoft.com. Vous pouvez continuer à utiliser ce nom de domaine initial, ou vous pouvez ajouter votre propre nom de domaine personnalisé au service cloud. Cette rubrique explique comment ajouter votre propre nom de domaine et comporte des informations connexes.

Les utilisateurs Office 365 peuvent également être intéressés par les rubriques suivantes :

- [Notions DNS de base](https://support.office.com/article/854b6b2b-0255-4089-8019-b765cff70377)
- [Rechercher mon bureau d’enregistrement de domaines ou mon fournisseur d’hébergement DNS](https://support.office.com/article/Find-your-domain-registrar-or-DNS-hosting-provider-b5b633ba-1e56-4a98-8ff5-2acaac63a5c8/)
- [Utiliser les noms de domaine dans Office 365](https://support.office.com/article/Work-with-domain-names-in-Office-365-4f1bd681-337a-4bd3-a7b7-cf77b18d0870/)

## À propos de votre domaine onmicrosoft.com

Vous pouvez utiliser votre domaine onmicrosoft.com avec d’autres services. Par exemple, vous pouvez utiliser le domaine avec Exchange Online et Lync Online pour créer des listes de distribution et des comptes de connexion pour que les utilisateurs puissent accéder à SharePoint Online et aux collections de sites.

Si vous ajoutez vos propres noms de domaine à votre annuaire, vous pouvez continuer à utiliser votre domaine onmicrosoft.com.

Une fois choisi le nom à utiliser avec le service cloud pendant l’inscription, par exemple contoso.onmicrosoft.com, vous ne pouvez pas modifier le nom.

## Comment puis-je ajouter mon propre domaine ?

Si votre organisation possède déjà un nom de domaine personnalisé, en tant qu’administrateur, vous pouvez l’ajouter à votre annuaire Azure AD et l’utiliser avec tous les services en ligne Microsoft auxquels vous êtes abonné. Une fois que vous avez ajouté votre nom de domaine à Azure AD, vous pouvez commencer à associer ce nom de domaine à vos différents services cloud.

Vous pouvez ajouter jusqu’à 900 noms de domaine à votre client Azure AD à l’aide :

- du portail de gestion Azure, du portail Office 365 ou du portail Microsoft Intune ;
- du module Azure Active Directory pour Windows PowerShell. Pour plus d’informations sur les applets de commande à utiliser, consultez la page [Gérer les domaines](https://msdn.microsoft.com/library/azure/dn919677.aspx).

Vous devez déjà avoir enregistré un nom de domaine et disposer des informations de connexion nécessaires pour le bureau d’enregistrement de votre nom de domaine (par exemple, Go Daddy ou Register.com).

Vous pouvez ajouter plusieurs domaines à votre annuaire. Toutefois, vous ne pouvez pas ajouter le même domaine à des annuaires différents. Ainsi, par exemple, si vous ajoutez votre domaine à votre annuaire, vous ne pouvez pas créer un autre annuaire Azure AD et y ajouter le même nom de domaine.

Pour utiliser l’authentification unique avec le service cloud, nous vous recommandons de commencer à préparer votre environnement Active Directory en exécutant l’outil de préparation au déploiement Microsoft. Cet outil inspecte votre environnement Active Directory et fournit un rapport indiquant si vous êtes prêt à configurer l’authentification unique. Sinon, il répertorie les modifications à apporter pour préparer l’authentification unique. Par exemple, il examine si vos utilisateurs possèdent un nom d’utilisateur principal (UPN) et si cet UPN est au bon format. Pour télécharger cet outil, consultez [Outil de préparation au déploiement Microsoft](http://go.microsoft.com/fwlink/?linkid=235650).

> [AZURE.NOTE]Vous utilisez Office 365 ? Une fois que vous avez configuré votre domaine, vous pouvez commencer à créer des adresses de messagerie, des comptes Lync Online et des groupes de distribution qui utilisent le nom de votre domaine personnalisé. Vous pouvez également utiliser votre nom de domaine pour un site web public hébergé sur SharePoint Online.

- [Ajouter et vérifier un domaine à l’aide du portail de gestion Azure](#add-and-verify-a-domain-using-the-azure-management-portal)
- [Modifier des enregistrements DNS pour vos services cloud](#edit-dns-records-for-your-cloud-services)
- [Vérifier un domaine auprès de n’importe quel bureau d’enregistrement de noms de domaine](#verify-a-domain-at-any-domain-name-registrar)

### Ajouter et vérifier un domaine à l’aide du portail de gestion Azure

1. Dans le portail, cliquez sur **Active Directory**, puis sur le nom de l’annuaire de votre organisation. Vous pouvez effectuer l’une des opérations suivantes :
    1. Dans la page de répertoire par défaut, cliquez sur **Ajouter un domaine** dans la section **Améliorer l’expérience de connexion utilisateur**.
2. Cliquez sur **Domaines**, puis sur **Ajouter un domaine client** ou sur le bouton **Ajouter**.
2. Sur la page **Ajouter un domaine**, tapez le nom de domaine que vous souhaitez ajouter et effectuez l’une des opérations suivantes :
    1. Si vous ne souhaitez pas intégrer votre Active Directory local à Azure AD, procédez comme suit :
        1. Laissez la case **Je prévois de configurer ce domaine pour la connexion unique à mon annuaire Active Directory local** décochée, puis cliquez sur le bouton **Ajouter**.
        2. Une fois que votre domaine a été correctement ajouté à Azure AD, cliquez sur la flèche pour passer à la page suivante afin de vérifier votre domaine.
        3. Suivez alors les instructions pour vérifier que le nom de domaine que vous avez ajouté dans les étapes précédentes vous appartient. Pour obtenir des instructions détaillées, consultez la section Vérifier un domaine auprès de n’importe quel bureau d’enregistrement de noms de domaine.
    2. Si vous souhaitez intégrer votre Active Directory local à Azure AD, procédez comme suit :
        1. Veuillez cocher la case **Je prévois de configurer ce domaine pour la connexion unique à mon annuaire Active Directory local**, puis cliquez sur le bouton **Ajouter**.
        2. Une fois votre domaine correctement ajouté à Azure AD, cliquez sur la flèche pour passer à la page suivante, puis suivez les instructions pour configurer le domaine que vous avez ajouté pour l’authentification unique.

> [AZURE.NOTE]Une fois votre nom de domaine ajouté à Azure AD, vous pouvez modifier le nom de domaine par défaut pour les nouvelles adresses de messagerie. Pour plus d’informations, consultez [Comment puis-je modifier le nom de domaine principal pour les utilisateurs ?](#how-can-i-change-the-primary-domain-name-for-users?) Vous pouvez également modifier le profil d’un compte d’utilisateur existant pour mettre à jour l’adresse de messagerie (qui est également votre ID d’utilisateur) afin d’utiliser votre nom de domaine personnalisé à la place du domaine onmicrosoft.com.

### Modifier des enregistrements DNS pour vos services cloud

> [AZURE.NOTE]À l’aide de Microsoft Intune ? Vous n’avez pas besoin de modifier des enregistrements DNS pour le service cloud Microsoft Intune.

Après avoir ajouté et vérifié votre nom de domaine personnalisé, l’étape suivante consiste à modifier les enregistrements DNS auprès de votre bureau d’enregistrement de domaines ou d’un fournisseur d’hébergement DNS qui dirigent le trafic vers votre service cloud. Azure AD fournit les informations DNS dont vous avez besoin.

Si vous venez de terminer l’Assistant **Ajout d’un domaine**, cliquez sur **Configurer les enregistrements DNS**. Sinon, effectuez les étapes suivantes :

1. Dans le portail, dans le volet gauche, cliquez sur **Domaines**.
2. Selon le portail que vous utilisez, cliquez sur le nom de domaine que vous souhaitez configurer, puis cliquez sur **Paramètres DNS** ou **Afficher les paramètres DNS**. La page **Paramètres DNS** répertorie les enregistrements DNS pour le service cloud.

    Si vous souhaitez configurer un service qui n’apparaît pas sous l’onglet Paramètres DNS, vérifiez vos sélections de services de domaine pour vous assurer que vous avez choisi ce service pour ce nom de domaine. Pour modifier les paramètres, par exemple, pour ajouter Lync Online, consultez la section Spécifier les services que vous allez utiliser avec votre domaine.

3. Au niveau de votre site web de bureau d’enregistrement de noms de domaine, ajoutez les enregistrements nécessaires dans votre fichier DNS.

Généralement, il faut environ 15 minutes pour que vos modifications prennent effet. Cependant, il peut s’écouler 72 heures pour que l’enregistrement DNS créé soit propagé dans le système DNS. Si vous avez besoin d’afficher à nouveau ces paramètres d’enregistrement, sur la page **Domaines**, cliquez sur le domaine, puis sur la page **Propriétés du domaine**, cliquez sur l’onglet **Paramètres DNS**.

Pour vérifier l’état de votre domaine, sur la page **Domaines** cliquez sur le domaine, puis sur la page **Propriétés du domaine**, cliquez sur **Dépanner le domaine**.

### Vérifier un domaine auprès de n’importe quel bureau d’enregistrement de noms de domaine

Si vous disposez déjà d’un domaine enregistré auprès d’un bureau d’enregistrement de noms de domaine et que vous souhaitez le configurer pour qu’il fonctionne avec Azure AD, la vérification du domaine est nécessaire pour confirmer que vous possédez le domaine. Pour vérifier votre domaine, créez un enregistrement DNS auprès d’un bureau d’enregistrement de noms de domaine, ou à l’endroit où est hébergé votre DNS et Azure AD utilise cet enregistrement pour confirmer que vous possédez le domaine.

Avant de pouvoir vérifier votre domaine, vous devez ajouter un domaine personnalisé à Azure AD. Si vous avez ajouté un domaine personnalisé, mais que le domaine n’a pas encore été vérifié, l’état indique **Cliquez pour vérifier le domaine** ou **Non vérifié**.

#### Collecter des informations sur votre domaine 

Selon le portail que vous utilisez pour administrer votre annuaire Azure AD, vous devez collecter des informations sur votre domaine pour pouvoir créer ultérieurement un enregistrement DNS qui est utilisé pendant le processus de vérification.

Si vous utilisez Microsoft Intune ou le portail de compte Azure :

1. Sur la page **Domaines**, dans la liste des noms de domaine, recherchez le domaine que vous voulez vérifier. Dans la colonne **État**, cliquez sur **Cliquez pour vérifier le domaine**.
2. Sur la page **Vérifier le domaine**, dans la liste déroulante **Reportez-vous aux instructions pour exécuter cette étape avec :**, sélectionnez votre fournisseur d’hébergement DNS. Si votre fournisseur ne figure pas dans la liste, choisissez **Instructions générales**.
3. Dans la liste déroulante **Sélectionner une méthode de vérification :**, sélectionnez **Ajouter un enregistrement TXT (méthode préférée)** ou **Ajouter un enregistrement MX (autre méthode)**.

    Si votre fournisseur d’hébergement DNS vous permet de créer des enregistrements TXT, nous vous recommandons d’utiliser un enregistrement TXT pour la vérification. Pourquoi ? Les enregistrements TXT sont simples à créer et ne permettent pas d’interférer avec la remise du courrier électronique si une valeur incorrecte est entrée par accident.

4. À partir de la table, copiez ou enregistrez les informations **Adresse de destination ou de pointage**.

Si vous utilisez le portail de gestion :

1. Dans le portail, cliquez sur **Active Directory**, sur le nom de votre annuaire, puis sur **Domaines**. 
2. Sur la page **Domaines**, dans la liste des noms de domaine, cliquez sur le domaine que vous souhaitez vérifier, puis sur **Vérifier**.
2. Sur la page **Vérifier**, dans la liste déroulante **Type d’enregistrement**, sélectionnez **Enregistrement TXT** ou **Enregistrement MX**.
3. Copiez ou enregistrez les informations qu’il contient.

#### Ajouter un enregistrement DNS à votre bureau d’enregistrement de noms de domaine 

Azure AD utilise un enregistrement DNS que vous créez auprès de votre bureau d’enregistrement de noms de domaine pour confirmer que vous possédez le domaine. Utilisez les instructions ci-dessous pour créer un type d’enregistrement TXT ou MX pour un domaine inscrit auprès de votre bureau d’enregistrement.

Si votre bureau d’enregistrement de domaines n’accepte pas « @ » comme nom d’hôte, contactez votre bureau d’enregistrement de domaines pour savoir comment représenter le « parent de la zone actuelle ».

Office 365 dispose d’[instructions spécifiques pour les bureaux d’enregistrement de domaines courants](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

Pour obtenir des instructions générales, procédez comme suit pour ajouter un enregistrement TXT ou MX :

1. Connectez-vous au site web de votre bureau d’enregistrement de domaines, puis sélectionnez le domaine que vous voulez vérifier.
2. Dans la zone de gestion DNS de votre compte, sélectionnez l’option Ajouter un enregistrement TXT ou MX pour votre domaine.
3. Dans la zone **TXT** ou **MX** du domaine, tapez ce qui suit : @
4. Dans la zone **Nom de domaine complet (FQDN)** ou **Pointe vers** tapez ou collez l’**Adresse de destination ou de pointage** que vous avez enregistrée à l’étape précédente.
5. Pour un enregistrement TXT, les informations **TTL** sont demandées, tapez **1** pour définir TTL sur une heure. 

    Pour un enregistrement MX, une priorité (ou préférence) est demandée, tapez un nombre supérieur au nombre que vous avez spécifié pour les enregistrements MX existants. Vous pouvez ainsi empêcher le nouvel enregistrement MX d’interférer avec le routage du courrier du domaine. Au lieu d’une priorité, vous pouvez voir les options suivantes : **Faible**, **Moyen**, **Élevé**. Dans ce cas, choisissez **Faible**.

6. Enregistrez vos modifications, puis déconnectez-vous du site web de votre bureau d’enregistrement de noms de domaine.

Une fois créé l’enregistrement TXT ou l’enregistrement MX et une fois effectuée l’authentification sur le site web, revenez au service cloud pour vérifier le domaine. Généralement, il faut environ 15 minutes pour que vos modifications prennent effet. Toutefois, il peut s’écouler 72 heures pour que l’enregistrement DNS créé soit propagé dans le système DNS.

#### Vérification de votre domaine 

Une fois l’enregistrement que vous avez créé pour votre domaine propagé dans le système DNS, procédez comme suit pour terminer la vérification de votre domaine avec Azure AD.

1. Dans le portail, cliquez sur **Domaines**.
2. Dans la liste **Domaines**, recherchez le domaine que vous vérifiez et, selon le portail que vous utilisez, cliquez sur **Cliquez pour vérifier le domaine** ou **Vérifiez**.
3. Suivez les instructions fournies pour terminer le processus de vérification.
    - Si la vérification du domaine réussit, vous êtes averti que votre domaine a été ajouté à votre compte.
    - En cas d’échec de la vérification du domaine, les modifications que vous avez effectuées auprès du bureau d’enregistrement de domaines peuvent nécessiter du temps pour se propager. Annulez le processus de vérification et revenez plus tard pour réessayer.

S’il s’est écoulé plus de 72 heures depuis que vous avez apporté des modifications à votre domaine, connectez-vous au site web du bureau d’enregistrement de domaines et vérifiez que vous avez correctement entré les informations d’alias. Si vous avez entré les informations de manière incorrecte, vous devez supprimer l’enregistrement DNS incorrect et en créer un autre avec les informations correctes en utilisant les procédures de cette rubrique.

Une fois votre domaine vérifié, vous pouvez le configurer pour qu’il fonctionne avec vos comptes.

## Comment puis-je modifier le nom de domaine principal pour les utilisateurs ?

Après avoir ajouté votre nom de domaine à Azure AD, vous pouvez modifier le nom de domaine qui doit s’afficher par défaut quand vous créez un compte d’utilisateur. Pour ce faire, procédez comme suit :

1. Sur la page du portail, en haut à gauche, cliquez sur le nom de votre organisation.
2. Cliquez sur **Modifier**.
3. Choisissez un nouveau nom de domaine par défaut, tel que le nom de domaine personnalisé que vous avez ajouté.

## Comment puis-je supprimer un domaine ?

Avant de supprimer un nom de domaine, nous vous recommandons de lire les informations suivantes :

- Le nom de domaine contoso.onmicrosoft.com d’origine qui a été fourni pour votre annuaire au moment de l’inscription ne peut pas être supprimé. 
- Vous ne pouvez pas supprimer un domaine de niveau supérieur ayant des sous-domaines associés tant que les sous-domaines n’ont pas été supprimés. Par exemple, vous ne pouvez pas supprimer adatum.com si corp.adatum.com ou un autre sous-domaine utilise le nom de domaine de niveau supérieur. Pour plus d’informations, consultez l’[article de support](https://support.microsoft.com/kb/2787792/).
- Avez-vous activé la synchronisation d’annuaires ? Si oui, un domaine a été automatiquement ajouté à votre compte et ressemble à ceci : contoso.mail.onmicrosoft.com. Ce nom de domaine ne peut pas être supprimé.
- Avant de pouvoir supprimer un nom de domaine, vous devez d’abord supprimer le nom de domaine de tous les utilisateurs ou les comptes de messagerie électronique associés à ce domaine. Vous pouvez supprimer tous les comptes ou réaliser des modifications en bloc sur les comptes d’utilisateurs pour changer leurs informations de nom de domaine et leurs adresses électroniques. Pour plus d’informations, consultez la rubrique [Création ou modification d’utilisateurs dans Azure AD](active-directory-create-users.md).
- Si vous hébergez un site SharePoint Online sur un nom de domaine qui est utilisé pour une collection de sites SharePoint Online, vous devez supprimer la collection de sites avant de pouvoir supprimer le nom de domaine.

Pour supprimer un nom de domaine :

1. Sur la page du portail, dans le volet gauche, cliquez sur **Domaines**.
2. Sur la page **Domaines** sélectionnez le nom de domaine que vous souhaitez supprimer, puis cliquez sur **Supprimer le domaine**.
3. Sur la page **Supprimer le domaine**, cliquez sur **Oui**.

Si le nom de votre domaine ne peut pas être supprimé à ce stade, l’état du nom de domaine est Suppression en attente sur la page Domaines. Si vous continuez à voir cet état, réessayez de supprimer le nom de domaine.

## Résolution des problèmes après avoir modifié votre nom de domaine 

### J’ai modifié mon domaine, mais il n’affiche pas encore les modifications.

Du fait de l’application des mises à jour via le système de noms de domaine (DNS), il peut s’écouler jusqu’à 72 heures avant que les modifications apportées à un bureau d’enregistrement de domaines ou à un fournisseur d’hébergement se propagent entièrement via Internet. Ensuite, vous pouvez commencer à utiliser votre nom de domaine avec vos services.

En outre, les modifications que vous apportez au niveau du bureau d’enregistrement de domaines doivent être réellement correctes. Si vous revenez en arrière pour corriger une erreur, il peut s’écouler plusieurs jours avant que le paramètre mis à jour apparaisse sur le site du portail du service cloud.

Combien de temps faut-il ? Cela dépend en partie de la durée de vie du paramètre (TTL, Time To Live) que vous avez spécifié pour l’enregistrement DNS que vous remplacez ou mettez à jour. Jusqu’à l’expiration de la durée de vie, les serveurs Internet qui ont mis en cache les données précédentes n’interrogent pas le serveur de noms faisant autorité pour demander la nouvelle valeur.

### J’ai ajouté un domaine, l’ai vérifié et ai configuré les enregistrements DNS sur le site du bureau d’enregistrement de domaines. Pourquoi les nouveaux comptes de messagerie ne reçoivent-ils pas encore de courrier ? 

Une fois que vous avez terminé d’ajouter ou de mettre à jour des enregistrements DNS pour votre domaine, il peut s’écouler jusqu’à 72 heures pour que les modifications prennent effet.

En outre, les informations des paramètres doivent être réellement correctes sur le site du bureau d’enregistrement de domaines. Vérifiez vos paramètres et assurez-vous d’avoir prévu suffisamment de temps pour que les enregistrements DNS modifiés soient propagés dans le système.

### Je ne peux pas vérifier mon nom de domaine. Comment puis-je savoir ce qui ne va pas ? 

Pour détecter les problèmes, vous pouvez utiliser l’Assistant Résolution des problèmes des domaines. Pour démarrer l’Assistant, procédez comme suit : dans le portail du service cloud, sur la page Admin, cliquez sur **Domaines**, puis double-cliquez sur le nom de domaine que vous souhaitez vérifier. Ensuite, sous **Résolution des problèmes**, cliquez sur **Dépanner le domaine**.

L’Assistant Résolution des problèmes vous demande où vous vous trouvez dans le processus de vérification, puis fournit des informations pour vous permettre de terminer la vérification.

### J’ai ajouté et vérifié mon domaine, mais le nouveau nom de domaine ne fonctionne pas pour les adresses de messagerie existantes des utilisateurs. 

Si vous ajoutez votre nom de domaine personnalisé au service cloud après avoir ajouté des comptes d’utilisateurs, vous devrez effectuer des mises à jour pour utiliser le nouveau nom de domaine. Par exemple, vous devez modifier les comptes d’utilisateurs pour définir leurs adresses de messagerie afin qu’elles utilisent votre domaine personnalisé.

## Étapes suivantes

- [Forum Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD)
- [Stackoverflow](http://stackoverflow.com/questions/tagged/azure)
- [Inscription à Azure en tant qu’organisation](sign-up-organization.md)
- [Gestion de domaines dans Azure AD](https://msdn.microsoft.com/library/azure/dn919677.aspx) 

<!---HONumber=August15_HO6-->