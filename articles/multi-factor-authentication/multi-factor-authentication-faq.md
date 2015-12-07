<properties 
	pageTitle="Forum Aux Questions d’Azure Multi-Factor Authentication" 
	description="Fournit une liste de questions fréquentes et les réponses relatives à Azure Multi-factor authentication (MFA). MFA est une méthode permettant de vérifier votre identité qui requiert l'utilisation d'autres méthodes que le nom d'utilisateur et le mot de passe. Ce service fournit une couche de sécurité supplémentaire pour les connexions et les transactions de l'utilisateur." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/20/2015" 
	ms.author="billmath"/>

# Forum Aux Questions d’Azure Multi-Factor Authentication


Ce forum aux questions répond aux questions sur Azure Multi-Factor Authentication. Ce FAQ couvre les questions sur l'utilisation du service, notamment le modèle de facturation et la facilité d'utilisation.

## Généralités

**Q : Comment puis-je obtenir de l'aide pour Azure Multi-Factor Authentication ?**

[Recherche dans la Base de connaissances Microsoft (KB)](http://search.microsoft.com/supportresults.aspx?form=mssupport&q=phonefactor)

- Recherchez des solutions techniques aux problèmes courants couverts par la garantie de réparation et d'assistance, relatifs à la prise en charge du serveur Microsoft Azure Multi-Factor Authentication (facteur téléphonique) dans la Base de connaissances Microsoft (KB).

[Forums Microsoft Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/home?forum=WindowsAzureAD)

- Vous pouvez naviguer et rechercher des questions et des réponses techniques de la communauté ou poser votre question en cliquant [ici](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

[Réinitialisation de mot de passe](mailto:phonefactorsupport@microsoft.com)

- Les clients Phonefactor qui ont des questions sur la réinitialisation de leur mot de passe ou qui veulent obtenir de l'aide pour réinitialiser leur mot de passe peuvent utiliser le lien ci-dessous pour ouvrir une demande de support.

[Support technique du serveur Microsoft Azure Multi-Factor Authentication (facteur téléphonique)](https://support.microsoft.com/oas/default.aspx?prid=14947)

- Utilisez ce lien pour contacter un professionnel du support Microsoft. Nous allons vous poser quelques questions pour nous aider à déterminer vos options de support. Les options de support peuvent inclure le courrier électronique, les demandes en ligne ou le téléphone. 

[Questions générales sur le serveur Microsoft Azure Multi-Factor Authentication (facteur téléphonique)](http://azure.microsoft.com/services/multi-factor-authentication)

- Pour en savoir plus sur le serveur Microsoft Azure Multi-Factor Authentication (facteur téléphonique) ou si vous avez des questions sur l'achat de produits et les différentes options de support disponibles, veuillez visiter le site ou envoyer un e-mail à [pfsales@microsoft.com](mailto:pfsales@microsoft.com). 



**Q : Comment les données utilisateur sont-elles gérées par le serveur Azure Multi-Factor Authentication ?**

Lorsque vous utilisez le serveur Multi-Factor Authentication (MFA) sur site, les données utilisateur sont stockées sur les serveurs locaux. Aucune donnée utilisateur persistante n'est stockée dans le cloud. Lorsque l'utilisateur effectue une authentification à deux facteurs, le serveur MFA envoie des données au service cloud Azure MFA pour effectuer l'authentification. Lorsque ces demandes d'authentification sont envoyées au service cloud, les champs suivants sont envoyés dans la demande et les journaux afin qu'ils soient disponibles dans les rapports d'utilisation/d'authentification du client. Certains champs étant facultatifs, ils peuvent être activés ou désactivés sur le serveur Multi-Factor Authentication. La communication du serveur MFA au service cloud MFA utilise SSL/TLS sur le port 443 sortant. Ces champs sont les suivants :

- ID unique : nom d'utilisateur ou ID du serveur MFA interne
- Prénom et nom : facultatif
- Adresse de messagerie : facultatif
- Numéro de téléphone : en cas d'authentification par appel vocal ou SMS
- Jeton du périphérique : en cas d'authentification par application mobile
- Mode d'authentification 
- Résultat de l'authentification 
- Nom du serveur MFA 
- Adresse IP du serveur MFA 
- Adresse IP du client (si disponible)



En plus des champs ci-dessus, le résultat de l'authentification (réussite/échec) et le motif de refus sont également stockés avec les données d'authentification et disponibles dans les rapports d'utilisation/d'authentification.




## Facturation

**Q : Mon organisation devra-t-elle payer les appels téléphoniques ou les SMS utilisés pour authentifier mes utilisateurs ?**

Tous les frais sont regroupés dans le coût du service par utilisateur ou par authentification. Les appels émis et les SMS envoyés aux utilisateurs utilisant Azure Multi-Factor Authentication ne sont pas facturés aux organisations. Les propriétaires de téléphones peuvent avoir à supporter des frais liés à l’itinérance ou d'autres coûts de leurs opérateurs téléphoniques pour les appels téléphoniques ou les messages reçus.

**« Q : Comment le service Azure Multi-Factor Authentication est-il facturé à mon organisation ? »**

Vous choisissez un modèle de facturation/d’utilisation « par utilisateur » ou « par authentification » lors de la création d'un fournisseur d'authentification multifacteur dans le portail de gestion Azure. Il s’agit d’une ressource basée sur la consommation facturée sur l’abonnement Azure de l'organisation, tout comme les machines virtuelles, les sites web, etc. sont facturés sur l'abonnement.

**Q : Le modèle de facturation « par utilisateur » dépend-il du nombre d'utilisateurs activés pour l'authentification multifacteur ou du nombre d'utilisateurs qui effectuent des vérifications ?**

La facturation repose sur le nombre d'utilisateurs activés pour l'authentification multifacteur.

## Facilité d'utilisation

**Q : Que faire si je ne reçois pas de réponse ou si j'ai oublié mon téléphone ?**

Si vous avez configuré un téléphone de secours, réessayez en sélectionnant ce téléphone sur la page de connexion. Si vous n'avez pas d’autre méthode configurée, contactez votre administrateur et demandez-lui de mettre à jour le numéro attribué à votre téléphone principal : mobile ou de bureau.

**Q : J'ai supprimé un utilisateur d'un rôle d'administrateur mais j'ai oublié de désactiver l'authentification multifacteur et maintenant elle n'est pas affichée dans la liste. Comment supprimer cette fonctionnalité ?**

- Selon le portail que vous utilisez, dans le volet gauche, cliquez sur Utilisateurs ou Utilisateurs et groupes.
- Cochez la case en regard de l'utilisateur que vous souhaitez modifier, puis cliquez sur Modifier ou sur l'icône de modification.
- Cliquez sur Paramètres, sous Affecter un rôle, cliquez sur Oui et réaffectez le rôle d’administrateur à l'utilisateur.
- Accédez à la page d'authentification multifacteur. Le compte doit maintenant figurer dans la liste sur la page. 
- Suivez les étapes ci-dessus pour désactiver l'authentification multifacteur pour un compte ? 
- À ce stade, vous pouvez supprimer le compte du rôle d'administrateur.


**Q : Que faire si un utilisateur dont le compte a été verrouillé me contacte (administrateur) ?**

Vous pouvez réinitialiser l'utilisateur en lui faisant effectuer le processus d'inscription à nouveau. Pour ce faire, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md)

**Q : Que faire si un utilisateur utilisant des mots de passe d'application a perdu un appareil ou s'il a été victime d'un vol ?**

Vous pouvez supprimer tous les mots de passe de l’application utilisateur afin d'empêcher tout accès non autorisé. Une fois que vous disposez d'un périphérique de remplacement, l'utilisateur peut les recréer. Pour ce faire, consultez [Gestion des paramètres utilisateur et des appareils avec Azure Multi-Factor Authentication dans le cloud](multi-factor-authentication-manage-users-and-devices.md)

**Q : Que se passe-t-il si l'utilisateur n'est pas en mesure de se connecter à des applications sans navigateur ?**

- Les utilisateurs avec l'authentification multifacteur nécessitent un mot de passe d’application pour se connecter à certaines applications sans navigateur.
- Les utilisateurs doivent effacer les informations de connexion (supprimer les informations de connexion), redémarrer l'application et se connecter à l'aide de leur mot de passe et de leur nom d'utilisateur d'application. 

Pour plus d'informations sur la création de mots de passe d'application, consultez [Aide relative aux mots de passe d'application](multi-factor-authentication-end-user-app-passwords.md)


>[AZURE.NOTE]Authentification moderne pour les clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prennent désormais en charge de nouveaux protocoles d’authentification et ils peuvent être activés pour prendre en charge Multi-Factor Authentication. Ainsi, une fois activés, les mots de passe d’application ne sont pas obligatoires avec les clients Office 2013. Pour plus d’informations, consultez la page [Version préliminaire publique de l'authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**Q : Que faire si je ne reçois pas de message texte ou si je réponds à un message texte bidirectionnel après expiration de la vérification ?**

Le service Azure Multi-Factor Authentication envoie des messages via des agrégateurs de SMS. De nombreux facteurs peuvent affecter la fiabilité de remise et de réception des messages, notamment l'agrégation utilisée, le pays de destination, l’opérateur de téléphonie mobile et la puissance du signal. Par conséquent, la remise de messages texte et la réception des SMS de réponse en cas de SMS bidirectionnel ne sont pas garanties. L’utilisation de SMS unidirectionnels est recommandée en lieu et place des SMS bidirectionnels, lorsque cela est possible, car elle est plus fiable et évite aux utilisateurs des frais appliqués à la réponse à un message envoyé depuis un autre pays.

Les vérifications de SMS sont également plus fiables dans certains pays comme les États-Unis et le Canada. Les utilisateurs qui rencontrent des difficultés pour recevoir des messages lors de l'utilisation de l'authentification multifacteur Azure sont invités à sélectionner les méthodes d'application ou de téléphone mobiles à la place. L'application mobile est formidable, car les notifications de l'application mobile peuvent être reçues via les connexions cellulaires et Wi-Fi, et le mot de passe d'application mobile s'affiche même si le périphérique n'a aucun signal. L'application Azure Authenticator est disponible pour [Windows Phone](http://www.windowsphone.com/store/app/azure-authenticator/03a5b2bf-6066-418f-b569-e8aecbc06e50), [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) et [IOS](https://itunes.apple.com/us/app/azure-authenticator/id983156458).

**Q : puis-je utiliser des jetons matériels avec le serveur MFA Azure ?**

Si vous utilisez le serveur MFA Azure, les jetons OATH TOTP tiers peuvent être importés et utilisés pour l'authentification Multifacteur (MFA). Nous prenons actuellement en charge l'importation de jetons OATH TOTP tiers dans un format PSKC plus ancien que Gemalto peut produire pour leurs jetons et l'importation de jetons au format CSV. Lorsque vous importez des jetons au format CSV, le fichier CSV doit contenir un numéro de série, la clé secrète au format Base32 et un intervalle de temps (généralement 30 secondes).

Par conséquent, si les jetons ActiveIdentity sont des jetons OATH TOTP, et si vous pouvez obtenir le fichier de clé de secrète dans un fichier CSV qui peut être importé dans le serveur MFA Azure, vous pourrez les utiliser. Les jetons OATH peuvent être utilisés avec AD FS, avec RADIUS lorsque le système client peut traiter les réponses de challenge d'accès et avec l'authentification basée sur les formulaires IIS.


## Erreurs

**Q : Que faire lorsqu'une erreur « La demande d'authentification ne concerne pas un compte activé » s'affiche lorsque je m'authentifie à l'aide des notifications de l'application mobile ?**

- Accédez à [https://account.activedirectory.windowsazure.com/profile/](https://account.activedirectory.windowsazure.com/profile/) et connectez-vous avec votre compte professionnel.
- Si nécessaire, cliquez sur Autres options de vérification et sélectionnez une option différente pour terminer la vérification de votre compte.
- Cliquez sur Vérification de sécurité supplémentaire.
- Supprimez le compte existant de votre application mobile.
- Cliquez sur Configurer et suivez les instructions pour configurer à nouveau l'application mobile.




**Q : Que faire lorsqu'une erreur 0x800434D4L se produit lors de la connexion à l'aide d'une application sans navigateur ?**

Actuellement, une vérification de sécurité supplémentaire est utilisable uniquement avec les applications et services accessibles via votre navigateur. Les applications sans navigateur (également appelées applications clientes riches) qui sont installées sur votre ordinateur local, comme Windows Powershell, ne fonctionnent pas avec les comptes requis pour la vérification de sécurité supplémentaire. Dans ce cas, l'application peut générer l'erreur 0x800434D4L.

La solution consiste à disposer d'un compte d'utilisateur distinct pour les opérations liées aux administrateurs et les autres. Vous pouvez ultérieurement lier les boîtes de réception de votre compte d'administrateur et d’un compte non administrateur pour vous connecter à Outlook à l'aide de votre compte non administrateur. Pour plus d'informations, consultez [Permettre à un administrateur d'ouvrir et d'afficher le contenu de la boîte aux lettres d'un utilisateur] (http://help.outlook.com/141/gg709759(d=loband).aspx?sl=1).

<!---HONumber=AcomDC_1125_2015-->