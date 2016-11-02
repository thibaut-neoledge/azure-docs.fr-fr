<properties
	pageTitle="Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise | Microsoft Azure"
	description="Découvrez comment configurer l’authentification unique entre Azure Active Directory et Qlik Sense Enterprise."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/31/2016"
	ms.author="jeedes"/>


# Didacticiel : Intégration d’Azure Active Directory à Qlik Sense Enterprise

Dans ce didacticiel, vous allez apprendre à intégrer Qlik Sense Enterprise à Azure Active Directory (Azure AD).

L’intégration du logiciel Qlik Sense Enterprise à Azure AD vous offre les avantages suivants :

- Dans Azure AD, vous pouvez contrôler qui a accès à Qlik Sense Enterprise.
- Vous pouvez autoriser vos utilisateurs à se connecter automatiquement à Qlik Sense Enterprise (via l’authentification unique) avec leur compte Azure AD.
- Vous pouvez gérer vos comptes à un emplacement central : le portail Azure Classic.

Pour en savoir plus sur l’intégration des applications SaaS avec Azure AD, consultez [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md).

## Composants requis

Pour configurer l’intégration d’Azure AD à Qlik Sense Enterprise, vous avez besoin des éléments suivants :

- Un abonnement Azure AD
- Un abonnement Qlik Sense Enterprise pour lequel l’authentification unique est activée


> [AZURE.NOTE] Pour tester les étapes de ce didacticiel, nous déconseillons l’utilisation d’un environnement de production.


Vous devez en outre suivre les recommandations ci-dessous :

- Vous ne devez pas utiliser votre environnement de production, sauf si cela est nécessaire.
- Si vous n’avez pas d’environnement d’essai Azure AD, vous pouvez obtenir un essai d’un mois [ici](https://azure.microsoft.com/pricing/free-trial/).


## Description du scénario
Dans ce didacticiel, vous testez l’authentification unique Azure AD dans un environnement de test.

Le scénario décrit dans ce didacticiel se compose des deux sections principales suivantes :

1. Ajout de Qlik Sense Enterprise à partir de la galerie
2. Configuration et test de l’authentification unique Azure AD


## Ajout de Qlik Sense Enterprise à partir de la galerie
Pour configurer l’intégration de Qlik Sense Enterprise à Azure AD, vous devez ajouter Qlik Sense Enterprise depuis la galerie à votre liste d’applications SaaS gérées.

**Pour ajouter Qlik Sense Enterprise à partir de la galerie, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Active Directory][1]
2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour ouvrir la vue des applications, dans la vue d'annuaire, cliquez sur **Applications** dans le menu du haut.

	![Applications][2]

4. Cliquez sur **Ajouter** en bas de la page.

	![Applications][3]

5. Dans la boîte de dialogue **Que voulez-vous faire ?**, cliquez sur **Ajouter une application à partir de la galerie**.

	![Applications][4]

6. Dans la zone de recherche, tapez **Qlik Sense Enterprise**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_01.png)

7. Dans le volet de résultats, sélectionnez **Qlik Sense Enterprise**, puis cliquez sur **Terminer** pour ajouter l’application.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_02.png)

##  Configuration et test de l’authentification unique Azure AD
Dans cette section, vous allez configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise avec un utilisateur de test appelé « Britta Simon ».

Pour que l’authentification unique fonctionne, Azure AD doit savoir qui est l’utilisateur Qlik Sense Enterprise équivalent dans Azure AD. En d’autres termes, une relation entre un utilisateur Azure AD et l’utilisateur Qlik Sense Enterprise associé doit être établie.

Pour ce faire, affectez la valeur de **nom d’utilisateur** dans Azure AD comme valeur de **nom d’utilisateur** dans Qlik Sense Enterprise.

Pour configurer et tester l’authentification unique Azure AD avec Qlik Sense Enterprise, vous devez suivre les indications des sections suivantes :

1. **[Configuration de l’authentification unique Azure AD](#configuring-azure-ad-single-sign-on)** pour permettre à vos utilisateurs d’utiliser cette fonctionnalité.
2. **[Création d’un utilisateur de test Azure AD](#creating-an-azure-ad-test-user)** pour tester l’authentification unique Azure AD avec Britta Simon.
3. **[Création d’un utilisateur de test Qlik Sense Enterprise](#creating-a-qliksense-enterprise-test-user)** pour avoir un équivalent de Britta Simon dans Qlik Sense Enterprise, lié à la représentation Azure AD associée.
4. **[Affectation d’un utilisateur de test Azure AD](#assigning-the-azure-ad-test-user)** pour permettre à Britta Simon d’utiliser l’authentification unique Azure AD.
5. **[Test de l’authentification unique](#testing-single-sign-on)** pour vérifier si la configuration fonctionne.

### Configuration de l’authentification unique Azure AD

Dans cette section, vous allez activer l’authentification unique Azure AD dans le portail Azure Classic et configurer l’authentification unique dans votre application Qlik Sense Enterprise.


**Pour configurer l’authentification unique Azure AD avec Qlik Sense Enterprise, procédez comme suit :**

1. Dans la page d’intégration d’applications **Qlik Sense Enterprise** du portail Azure Classic, cliquez sur **Configurer l’authentification unique** pour ouvrir la boîte de dialogue **Configurer l’authentification unique**.
	 
	![Configurer l’authentification unique][6]

2. Dans la page **Comment voulez-vous que les utilisateurs se connectent à Qlik Sense Enterprise ?**, sélectionnez **Authentification unique avec Microsoft Azure AD**, puis cliquez sur **Suivant**.

	![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_03.png)

3. Sur la page **Configurer les paramètres d’application**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_04.png)

    a. Dans la zone de texte **URL de connexion**, tapez l’URL utilisée par vos utilisateurs pour se connecter à votre application Qlik Sense Enterprise, au format suivant : **https://\<Nom d’hôte complet Qlik Sense>:443/<Préfixe de proxy virtuel>/samlauthn/**.
	
	> [AZURE.NOTE] Notez la barre oblique à la fin de cet URI. Elle est obligatoire.

	b. Cliquez sur **Next**
 
4. Dans la page **Configurer l’authentification unique sur Qlik Sense Enterprise**, procédez comme suit :

	![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_05.png)

    a. Cliquez sur **Télécharger les métadonnées**, puis enregistrez le fichier sur votre ordinateur. Soyez prêt à modifier ce fichier de métadonnées avant de le charger vers le serveur Qlik Sense.

    b. Cliquez sur **Suivant**.

5. Préparez le fichier XML de métadonnées de fédération afin de le charger vers le serveur Qlik Sense.

	> [AZURE.NOTE] Avant de charger les métadonnées IdP vers le serveur Qlik Sense, vous devez modifier le fichier et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense.

	![QlikSense][qs24]

	a. Ouvrez le fichier FederationMetaData.xml téléchargé à partir d’Azure dans un éditeur de texte.

	b. Recherchez la valeur **RoleDescriptor**. Il en existe quatre entrées (deux paires de balises d’élément d’ouverture et de fermeture).

	c. Dans le fichier, supprimez les balises RoleDescriptor et toutes les informations insérées entre celles-ci.

	d. Enregistrez le fichier et conservez-le pour l’utiliser ultérieurement dans ce document.

6. Accédez à la console de gestion Qlik (QCM) de Qlik Sense en tant qu’utilisateur pouvant créer des configurations de proxy virtuel.

7. Dans la console QMC, cliquez sur l’élément de menu Virtual Proxy (Proxy virtuel).

	![QlikSense][qs6]

8. En bas de l’écran, cliquez sur le bouton Create new (Nouveau).

	![QlikSense][qs7]

9. L’écran Virtual proxy edit (Modification du proxy virtuel) s’affiche. Un menu permettant de rendre les options de configuration visibles est affiché sur le côté droit de l’écran.

	![QlikSense][qs9]

10. L’option de menu Identification étant sélectionnée, entrez les informations d’identification pour la configuration du proxy virtuel Azure.

	![QlikSense][qs8]
	
	a. Le champ Description contient un nom convivial pour la configuration du proxy virtuel. Entrez une valeur pour la description.
	
	b. Le champ Préfixe identifie le point de terminaison proxy virtuel pour la connexion à Qlik Sense avec l’authentification unique Azure AD. Entrez un nom de préfixe unique pour ce proxy virtuel.

	c. La valeur Session inactivity timeout (Délai d’inactivité de session) (minutes) représente le délai d’expiration des connexions via ce proxy virtuel.

	d. La zone Session cookie header name (Nom d’en-tête de cookie de session) est le nom du cookie stockant l’identificateur de session pour la session Qlik Sense qu’un utilisateur reçoit après une authentification réussie. Ce nom doit être unique.

11. Cliquez sur l’option de menu Authentification pour la rendre visible. L’écran Authentification s’affiche.

	![QlikSense][qs10]

	a. La liste déroulante **Anonymous access mode (Mode d’accès anonyme)** détermine si des utilisateurs anonymes peuvent accéder à Qlik Sense via le proxy virtuel. L’option par défaut est No anonymous user (Pas d’utilisateur anonyme).

	b. La liste déroulante **Authentication method (Méthode d’authentification)** détermine le schéma d’authentification qui sera utilisé par le proxy virtuel. Dans la liste déroulante, sélectionnez SAML. À la suite de cette sélection, d’autres options s’affichent.

	c. Dans le champ **SAML host URI** (URI d’hôte SAML), indiquez le nom d’hôte que les utilisateurs devront entrer pour accéder à Qlik Sense via ce proxy virtuel SAML. Le nom d’hôte est l’URI du serveur Qlik Sense.

	d. Dans le champ **SAML entity ID (ID d’entité SAML)**, entrez la valeur indiquée dans le champ SAML host URI (URI d’hôte SAML).

	e. La zone **SAML IdP metadata (Métadonnées IdP SAML)** indique le fichier modifié précédemment dans la section **Modifier les métadonnées de fédération dans la configuration d’Azure AD**. **Avant de charger les métadonnées IdP, vous devez modifier le fichier** et supprimer des informations pour vous assurer du bon fonctionnement entre Azure AD et le serveur Qlik Sense. **Reportez-vous aux instructions ci-dessus si le fichier doit encore être modifié**. Si le fichier a été modifié, cliquez sur le bouton Parcourir et sélectionnez le fichier de métadonnées modifié pour le charger vers la configuration du proxy virtuel.

	f. Entrez le nom d’attribut ou la référence de schéma pour l’attribut SAML représentant **l’ID utilisateur** qu’Azure AD enverra au serveur Qlik Sense. Les informations de référence de schéma sont disponibles dans les écrans de l’application Azure post-configuration. Pour utiliser l’attribut de nom, **entrez http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**.

	g. Entrez la valeur du **répertoire utilisateur** qui est attaché aux utilisateurs lorsqu’ils s’authentifient sur le serveur Qlik Sense via Azure AD. Les valeurs codées en dur doivent être entourées de **crochets**. Pour utiliser un attribut envoyé dans l’assertion SAML Azure AD, entrez son nom dans cette zone de texte **sans** crochets.

	h. **L’algorithme de signature SAML** définit la signature du certificat du fournisseur de service (dans ce cas le serveur Qlik Sense) pour la configuration du proxy virtuel. Si le serveur Qlik Sense utilise un certificat approuvé généré à l’aide de Microsoft Enhanced RSA and AES Cryptographic Provider, définissez l’algorithme de signature SAML sur **SHA-256**.

	i. La section SAML attribute mapping (Mappage d’attributs SAML) permet d’envoyer des attributs supplémentaires, comme des groupes, vers Qlik Sense pour les utiliser dans les règles de sécurité.

12. Cliquez sur l’option de menu Équilibrage de charge pour la rendre visible. L’écran Équilibrage de charge s’affiche.

	![QlikSense][qs11]

13. Cliquez sur le bouton Add new server node (Ajouter un nouveau nœud serveur), sélectionnez un ou plusieurs nœuds de moteur auxquels Qlik Sense enverra des sessions à des fins d’équilibrage de charge, puis cliquez sur le bouton Ajouter.

	![QlikSense][qs12]

14. Cliquez sur l’option de menu Avancé pour la rendre visible. L’écran Avancé s’affiche.

	![QlikSense][qs13]

	a. La liste des hôtes approuvés identifie les noms d’hôte qui sont acceptés lors de la connexion au serveur Qlik Sense. **Entrez le nom d’hôte spécifié par les utilisateurs pour se connecter au serveur Qlik Sense.** Le nom d’hôte est défini sur la même valeur que l’URI d’hôte SAML sans https://.

15. Cliquez sur le bouton Appliquer.

	![QlikSense][qs14]

16. Cliquez sur OK pour accepter le message d’avertissement indiquant que les proxys liés au proxy virtuel vont être redémarrés.

	![QlikSense][qs15]

17. Sur le côté droit de l’écran, le menu Éléments associés s’affiche. Cliquez sur l’option de menu Proxys.

	![QlikSense][qs16]

18. L’écran Proxy s’affiche. Cliquez sur le bouton Lier en bas de l’écran pour lier un proxy au proxy virtuel.

	![QlikSense][qs17]

19. Sélectionnez le nœud proxy qui prendra en charge cette connexion de proxy virtuel et cliquez sur le bouton Lier. Après l’établissement du lien, le proxy est répertorié dans les proxys associés.

	![QlikSense][qs18] ![QlikSense][qs19]

20. Après environ cinq à dix secondes, le message Refresh QMC (Actualiser la console QMC) s’affiche. Cliquez sur le bouton Refresh QMC (Actualiser la console QMC).

	![QlikSense][qs20]

21. Lors de l’actualisation de la console QMC, cliquez sur l’élément de menu Proxys virtuels. La nouvelle entrée de proxy virtuel SAML est indiquée dans le tableau affiché à l’écran. Cliquez sur l’entrée de proxy virtuel.

	![QlikSense][qs51]

22. En bas de l’écran, le bouton Download SP metadata (Télécharger les métadonnées du fournisseur de service) est activé. Cliquez sur le bouton Download SP metadata (Télécharger les métadonnées du fournisseur de service) pour enregistrer les métadonnées dans un fichier.

	![QlikSense][qs52]

23. Ouvrez le fichier de métadonnées du fournisseur de service. Observez les entrées **entityID** et **AssertionConsumerService**. Ces valeurs sont équivalentes à celles des champs **Identificateur** et **URL de connexion** dans la configuration de l’application Azure AD. Si elles ne correspondent pas, vous devez les remplacer dans Azure AD App configuration wizard (Assistant Configuration d’application Azure AD).

	![QlikSense][qs53]

24. Dans le portail Classic, sélectionnez la confirmation de la configuration de l’authentification unique, puis cliquez sur **Suivant**.
	
	![Authentification unique Azure AD][10]

25. Sur la page **Confirmation de l’authentification unique**, cliquez sur **Terminer**.
 
	![Authentification unique Azure AD][11]


### Création d’un utilisateur de test Azure AD
Dans cette section, vous allez créer un utilisateur de test appelé Britta Simon dans le portail Classic.


![Créer un utilisateur Azure AD][20]

**Pour créer un utilisateur de test dans Azure AD, procédez comme suit :**

1. Dans le volet de navigation gauche du **portail Azure Classic**, cliquez sur **Active Directory**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_09.png)

2. Dans la liste **Annuaire**, sélectionnez l'annuaire pour lequel vous voulez activer l'intégration d'annuaire.

3. Pour afficher la liste des utilisateurs, dans le menu du haut, cliquez sur **Utilisateurs**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_03.png)

4. Pour ouvrir la boîte de dialogue **Ajouter un utilisateur**, cliquez sur l’option **Ajouter un utilisateur** figurant dans la barre d’outils du bas.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_04.png)

5. Sur la page **Parlez-nous de cet utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_05.png)

    a. Dans Type d’utilisateur, sélectionnez Nouvel utilisateur dans votre organisation.

    b. Dans la zone de texte **Nom d’utilisateur**, entrez **BrittaSimon**.

    c. Cliquez sur **Next**.

6.  Sur la page **Profil utilisateur**, procédez comme suit : ![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_06.png)

    a. Dans la zone de texte **Prénom**, entrez **Britta**.

    b. Dans la zone de texte **Nom**, tapez **Simon**.

    c. Dans la zone de texte **Nom d’affichage**, entrez **Britta Simon**.

    d. Dans la liste **Rôle**, sélectionnez **Utilisateur**.

    e. Cliquez sur **Next**.

7. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, cliquez sur **créer**.

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_07.png)

8. Sur la page de boîte de dialogue **Obtenir un mot de passe temporaire**, procédez comme suit :

	![Création d’un utilisateur de test Azure AD](./media/active-directory-saas-qliksense-enterprise-tutorial/create_aaduser_08.png)

    a. Notez la valeur du **Nouveau mot de passe**.

    b. Cliquez sur **Terminé**.


### Création d’un utilisateur de test Qlik Sense Enterprise

Dans cette section, vous allez créer un utilisateur appelé Britta Simon dans Qlik Sense Enterprise. Collaborez avec l’équipe du support technique Qlik Sense Enterprise pour ajouter les utilisateurs à la plateforme Qlik Sense Enterprise.


### Affectation de l’utilisateur de test Azure AD

Dans cette section, vous allez autoriser Britta Simon à utiliser l’authentification unique Azure en lui accordant l’accès à Qlik Sense Enterprise.

![Affecter des utilisateurs][200]

**Pour affecter Britta Simon à Qlik Sense Enterprise, procédez comme suit :**

1. Pour ouvrir la vue des applications dans le portail Azure Classic, dans la vue de répertoire, cliquez sur l’option **Applications** figurant dans le menu du haut.

	![Affecter des utilisateurs][201]

2. Dans la liste des applications, sélectionnez **Qlik Sense Enterprise**.

	![Configurer l’authentification unique](./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_50.png)

3. Dans le menu situé en haut, cliquez sur **Utilisateurs**.

	![Affecter des utilisateurs][203]

4. Dans la liste Utilisateurs, sélectionnez **Britta Simon**.

5. Dans la barre d’outils située en bas, cliquez sur **Attribuer**.

	![Affecter des utilisateurs][205]


## Test de l’authentification unique

Dans cette section, vous allez tester la configuration de l’authentification unique Azure AD à l’aide du volet d’accès.

Lorsque vous cliquez sur la mosaïque Qlik Sense Enterprise dans le volet d’accès, vous devez être connecté automatiquement à votre application Qlik Sense Enterprise.


## Ressources supplémentaires

* [Liste de didacticiels sur l’intégration d’applications SaaS avec Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Qu’est-ce que l’accès aux applications et l’authentification unique avec Azure Active Directory ?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_general_205.png

[qs6]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs21]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_21.png
[qs22]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_22.png
[qs23]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_23.png
[qs24]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs25]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_25.png
[qs26]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_26.png
[qs51]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/active-directory-saas-qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png

<!---HONumber=AcomDC_0907_2016-->