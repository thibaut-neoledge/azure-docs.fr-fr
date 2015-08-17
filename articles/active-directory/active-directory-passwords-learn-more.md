<properties 
	pageTitle="En savoir plus : Gestion des mots de passe Azure AD | Microsoft Azure" 
	description="Rubriques avancées sur la gestion des mots de passe Azure AD, y compris le fonctionnement de l’écriture différée de mot de passe, la sécurité de l’écriture différée de mot de passe, le fonctionnement du portail de réinitialisation de mot de passe et les données utilisées par la réinitialisation de mot de passe." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# En savoir plus sur la gestion des mots de passe
Si vous avez déjà déployé la gestion des mots de passe ou si vous souhaitez simplement en savoir plus sur les aspects techniques avant de procéder au déploiement, cette section offre une vue d’ensemble parfaite. Nous traitons les aspects suivants :

* [**Vue d’ensemble de l’écriture différée de mot de passe**](#password-writeback-overview)
  - [Fonctionnement de l’écriture différée de mot de passe](#how-password-writeback-works)
  - [Scénarios pris en charge par l’écriture différée de mot de passe](#scenarios-supported-for-password-writeback)
  - [Modèle de sécurité de l’écriture différée de mot de passe](#password-writeback-security-model)
* [**Fonctionnement du portail de réinitialisation de mot de passe**](#how-does-the-password-reset-portal-work)
  - [Données utilisées par la réinitialisation de mot de passe](#what-data-is-used-by-password-reset)

## Vue d’ensemble de l’écriture différée de mot de passe
L’écriture différée de mot de passe est un composant [Azure Active Directory Connect](active-directory-aadconnect) qui peut être activé et utilisé par les abonnés actifs d’Azure Active Directory Premium. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

L’écriture différée de mot de passe vous permet de configurer votre client cloud pour l’écriture différée des mots de passe vers l’environnement Active Directory local. Cela vous évite d’avoir à configurer et à gérer une solution de réinitialisation de mot de passe libre-service locale complexe en offrant à vos utilisateurs un moyen pratique via le cloud de réinitialiser leurs mots de passe locaux, où qu’ils se trouvent. Voici certaines fonctionnalités importantes de l’écriture différée de mot de passe :

- **Retour d’informations immédiat.** L’écriture différée de mot de passe est une opération synchrone. Vos utilisateurs sont informés immédiatement si leur mot de passe ne remplit pas les critères des stratégies ou s’il n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
- **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs qui ont recours aux services AD FS ou à d’autres technologies de fédération.** Avec l’écriture différée des mots de passe, tant que les comptes des utilisateurs fédérés sont synchronisés dans votre client Azure AD, ils sont en mesure de gérer leurs mots de passe AD locaux à partir du cloud.
- **Prise en charge de la réinitialisation des mots de passe pour les utilisateurs qui ont recours à la synchronisation de hachage de mot de passe.** Lorsque le service de réinitialisation des mots de passe détecte qu’un compte d’utilisateur synchronisé est activé pour la synchronisation de hachage de mot de passe, le mot de passe local de ce compte et le mot de passe du cloud sont tous deux réinitialisés simultanément.
- **Prise en charge de la modification des mots de passe à partir du volet d’accès et d’Office 365.** Lorsque des utilisateurs fédérés ou disposant de la synchronisation du hachage de mot de passe modifient leurs mots de passe (ayant expiré ou non), nous réécrivons ces mots de passe dans votre environnement Active Directory local.
- **Prise en charge de l’écriture différée de mot de passe quand un administrateur les réinitialise à partir du** [**portail de gestion Azure**](https://manage.windowsazure.com). Chaque fois qu’un administrateur réinitialise le mot de passe d’un utilisateur dans le [portail de gestion Azure](https://manage.windowsazure.com), si cet utilisateur est fédéré ou dispose de la synchronisation de mot de passe, nous définissons également le mot de passe sélectionné par l’administrateur sur votre annuaire AD local. Cette fonctionnalité n’est pas prise en charge actuellement dans le portail d’administration Office.
- **Application de vos stratégies de mot de passe AD locales.** Lorsqu’un utilisateur réinitialise son mot de passe, nous vérifions qu’il est conforme à votre stratégie AD locale avant de le valider dans cet annuaire. Cela comprend l’historique, la complexité, l’âge, les filtres de mot de passe et toute autre restriction de mot de passe que vous avez définie dans votre annuaire Active Directory local.
- **Aucune règle de pare-feu pour le trafic entrant requise.** L’écriture différée des mots de passe utilise Azure Service Bus Relay comme canal de communication sous-jacent, ce qui signifie que vous n’avez pas à ouvrir de ports entrants sur votre pare-feu pour que cette fonctionnalité marche correctement. Seul le port 443 sortant doit être ouvert.
- **Aucune prise en charge pour les comptes d’utilisateur qui existent dans des groupes protégés dans votre annuaire Active Directory local.** Pour plus d’informations sur les groupes protégés, consultez la page [Comptes et groupes protégés dans Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### Fonctionnement de l’écriture différée des mots de passe
L’écriture différée des mots de passe comporte trois composants principaux :

- Service cloud de réinitialisation de mot de passe (également intégré aux pages de modification de mot de passe d’Azure AD)
- Azure Service Bus Relay spécifique au client
- Point de terminaison de réinitialisation de mot de passe local

Ces trois composants interagissent comme décrit dans le schéma ci-dessous :

  ![][001]

Lorsqu’un utilisateur fédéré ou disposant de la synchronisation du hachage de mot de passe réinitialise ou modifie son mot de passe dans le cloud, les événements suivants ont lieu :

1.	Nous vérifions le type de mot de passe de l’utilisateur. Si nous constatons que le mot de passe est géré localement, nous nous assurons que le service d’écriture différée est en cours d’exécution. Si c’est le cas, nous laissons l’utilisateur poursuivre. Dans le cas contraire, nous signalons à l’utilisateur que son mot de passe ne peut pas être réinitialisé ici.
2.	Ensuite, l’utilisateur passe par les portails d’authentification appropriés et atteint l’écran de réinitialisation de mot de passe.
3.	L’utilisateur sélectionne un nouveau mot de passe et le confirme.
4.	Lorsqu’il clique sur Envoyer, nous chiffrons le mot de passe en texte brut avec une clé publique qui a été créée durant le processus d’installation de l’écriture différée.
5.	Après avoir chiffré le mot de passe, nous l’incluons dans une charge utile qui est envoyée via un canal HTTPS à votre relais Service Bus spécifique au client (que nous configurons également pour vous lors du processus d’installation de l’écriture différée). Ce relais est protégé par un mot de passe généré de manière aléatoire, connu uniquement de votre installation locale.
6.	Une fois que le message a atteint Service Bus, le point de terminaison de réinitialisation de mot de passe sort de veille automatiquement et découvre qu’une demande de réinitialisation est en attente.
7.	Le service recherche alors l’utilisateur en question à l’aide de l’attribut d’ancrage de cloud. Pour que cette recherche réussisse, l’objet utilisateur doit exister dans l’espace de connecteur Active Directory et il doit être lié à l’objet MV correspondant et à l’objet de connecteur AAD correspondant. Pour finir, pour que la synchronisation trouve ce compte d’utilisateur, le lien de l’objet de connecteur Active Directory à MV doit avoir la règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx`. Celle-ci est nécessaire, car lorsque l’appel provient du cloud, le moteur de synchronisation utilise l’attribut cloudAnchor pour rechercher l’objet d’espace de connecteur AAD, puis il suit le lien vers l’objet MV, puis le lien vers l’objet Active Directory. Comme il peut y avoir plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le lien `Microsoft.InfromADUserAccountEnabled.xxx` pour choisir celui qui convient.
8.	Une fois le compte d’utilisateur trouvé, nous tentons de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée.
9.	Si l’opération de définition du mot de passe réussit, nous signalons à l’utilisateur que son mot de passe a été modifié et qu’il peut continuer.
10.	Si l’opération de définition du mot de passe échoue, nous renvoyons l’erreur à l’utilisateur et le laissons réessayer. L’opération peut échouer parce que le service est arrêté, parce que le mot de passe qu’il a sélectionné ne remplit pas les critères des stratégies de l’organisation, parce que nous n’avons pas trouvé l’utilisateur dans l’annuaire Active Directory local ou pour différentes autres raisons. Nous avons un message spécifique pour la plupart de ces cas de figure et nous indiquons à l’utilisateur ce qu’il peut faire pour résoudre le problème.

### Scénarios pris en charge par l’écriture différée des mots de passe
Le tableau ci-dessous décrit les scénarios pris en charge pour les différentes versions de nos fonctions de synchronisation. En général, nous vous recommandons vivement d’installer la dernière version d’[Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect) si vous souhaitez utiliser l’écriture différée de mot de passe.

  ![][002]

### Modèle de sécurité de l’écriture différée des mots de passe
L’écriture différée des mots de passe est un service hautement sécurisé et robuste. Pour garantir la protection de vos informations, nous activons un modèle de sécurité à quatre niveaux, décrit ci-dessous.

- **Relais Service Bus spécifique du client** : lorsque vous configurez le service, nous définissons un relais Service Bus spécifique au client et protégé par un mot de passe fort généré de manière aléatoire, auquel Microsoft n’a jamais accès.
- **Clé de chiffrement de mot de passe forte et verrouillée** : une fois le relais Service Bus créé, nous créons une paire de clés asymétriques forte qui nous permet de chiffrer le mot de passe lorsqu’il arrive sur le réseau. Cette clé réside uniquement dans le magasin de secrets du client dans le cloud, qui est fortement verrouillé et audité, comme n’importe quel mot de passe de l’annuaire.
- **Sécurité de couche de transport (TLS) standard** : lorsqu’une opération de réinitialisation ou de modification de mot de passe a lieu dans le cloud, nous prenons le mot de passe et nous le chiffrons avec votre clé publique. Ensuite, nous insérons cela dans un message HTTPS envoyé à votre relais Service Bus via un canal chiffré à l’aide de certificats SSL Microsoft. Une fois ce message arrivé dans Service Bus, votre agent local sort de veille, s’authentifie auprès de Service Bus à l’aide du mot de passe fort généré précédemment, récupère le message chiffré, le déchiffre à l’aide de la clé privée que nous avons générée et essaie ensuite de définir le mot de passe via l’API SetPassword AD DS. C’est cette étape qui nous permet d’appliquer votre stratégie de mot de passe Active Directory local (complexité, âge, historique, filtres, etc.) dans le cloud.
- **Stratégies d’expiration de message** : pour finir, si pour une raison quelconque le message reste dans Service Bus car votre service local est arrêté, le message est supprimé après quelques minutes, afin d’accroître encore davantage la sécurité.

## Fonctionnement du portail de réinitialisation de mot de passe
Quand un utilisateur accède au portail de réinitialisation de mot de passe, un flux de travail est lancé pour déterminer si ce compte d’utilisateur est valide, l’organisation à laquelle cet utilisateur appartient, où est géré le mot de passe de cet utilisateur et si l’utilisateur dispose d’une licence pour utiliser la fonctionnalité. Lisez les étapes ci-dessous pour en savoir plus sur la logique sous-jacente à la page de réinitialisation de mot de passe.

1.	L’utilisateur clique sur le lien Vous ne parvenez pas à accéder à votre compte ? ou il accède directement à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.	L’utilisateur entre un ID utilisateur et passe un test CAPTCHA.
3.	Azure AD vérifie si l’utilisateur peut utiliser cette fonctionnalité en procédant comme suit :
    - Il vérifie que cette fonctionnalité est activée pour l’utilisateur et qu’il possède une licence Azure AD.
        - Si ce n’est pas le cas, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
    - Vérifie que l’utilisateur a défini les données de test appropriées sur son compte, conformément à la stratégie de l’administrateur.
        - Si la stratégie n’exige qu’un seul test, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins un des tests activés par la stratégie de l’administrateur.
          - Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
        - Si la stratégie exige deux tests, AAD vérifie que l’utilisateur a défini les données appropriées pour au moins deux des tests activés par la stratégie de l’administrateur.
          - Si l’utilisateur n’est pas configuré, il est invité à contacter son administrateur pour réinitialiser son mot de passe.
    - Il vérifie si le mot de passe de l’utilisateur est géré localement (fédération ou synchronisation de hachage de mot de passe).
       - Si l’écriture différée est déployée et que le mot de passe est géré localement, l’utilisateur est autorisé à s’authentifier et à réinitialiser son mot de passe.
       - Si l’écriture différée n’est pas déployée et que le mot de passe est géré localement, l’utilisateur est invité à contacter son administrateur pour réinitialiser son mot de passe.
4.	S’il est établi que l’utilisateur est en mesure de réinitialiser son mot de passe, il reçoit alors des instructions pour mener à bien le processus de réinitialisation.

Pour en savoir plus sur le déploiement de l’écriture différée de mot de passe, consultez la page [Prise en main de la gestion des mots de passe Azure AD](active-directory-passwords-getting-started.md).

### Données utilisées par la réinitialisation de mot de passe
Le tableau suivant indique où et comment ces données sont utilisées lors de la réinitialisation de mot de passe. Il a pour but de vous aider à choisir les options d’authentification les plus appropriées pour votre organisation. Ce tableau recense également les formats à respecter si vous fournissez des données provenant de chemins d’entrée qui ne valident pas ces données pour le compte d’utilisateurs.

> [AZURE.NOTE]Le téléphone professionnel n’apparaît pas dans le portail d’inscription, car les utilisateurs ne sont pas actuellement en mesure de modifier cette propriété dans l’annuaire.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nom de la méthode de contact</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Élément de données Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Utilisé/définissable où&#160;?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Format requis</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone de bureau</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Utilisés dans&#160;:</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Peut être défini à partir de&#160;:</p>
              <p>PhoneNumber peut être défini à partir de PowerShell, de DirSync, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
										Doit contenir l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir l’indicatif régional (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir le symbole + devant l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir un espace entre l’indicatif du pays et le reste du numéro<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Les extensions ne sont pas prises en charge. Si des extensions sont spécifiées, elles seront supprimées avant la répartition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Téléphone mobile</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(AuthenticationPhone est utilisé si des données sont présentes. Sinon, le champ Téléphone portable est utilisé).</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>Utilisés dans&#160;:</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription</p>
              <p>Peut être défini à partir de&#160;: </p>
              <p>AuthenticationPhone peut être défini à partir du portail d’inscription de réinitialisation de mot de passe ou du portail d’inscription MFA.</p>
              <p>MobilePhone peut être défini à partir de PowerShell, de DirSync, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>+ ccc xxxyyyzzzz (par exemple, + 1 1234567890)</p>
              <ul>
                <li class="unordered">
										Doit contenir l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir l’indicatif régional (le cas échéant)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir le symbole + devant l’indicatif du pays<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Doit contenir un espace entre l’indicatif du pays et le reste du numéro<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Les extensions ne sont pas prises en charge. Si des extensions sont spécifiées, elles seront ignorées lors de la répartition de l’appel téléphonique.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Autre adresse de messagerie</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Authentication Email est utilisé si des données sont présentes. Sinon, le champ Adresse de messagerie de secours est utilisé).</p>
              <p>Remarque&#160;: le champ Adresse de messagerie de secours est spécifié comme un tableau de chaînes dans l’annuaire. Nous utilisons la première entrée de ce tableau.</p>
              <p>Par exemple, Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>Utilisés dans&#160;:</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription</p>
              <p>Peut être défini à partir de&#160;: </p>
              <p>AuthenticationEmail peut être défini à partir du portail d’inscription de réinitialisation de mot de passe ou du portail d’inscription MFA.</p>
              <p>AlternateEmail peut être défini à partir de PowerShell, du portail de gestion Azure et du portail d’administration Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> ou 甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
										Les adresses de messagerie doivent respecter la mise en forme standard.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
										Les adresses de messagerie Unicode sont prises en charge.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Questions et réponses de sécurité</p>
            </td>
            <td>
              <p>Non disponible pour une modification directe dans l’annuaire.</p>
            </td>
            <td>
              <p>Utilisés dans&#160;:</p>
              <p>Portail de réinitialisation du mot de passe</p>
              <p>Portail d’inscription </p>
              <p>Peut être défini à partir de&#160;: </p>
              <p>La seule façon de définir des questions de sécurité est de passer par le portail de gestion Azure.</p>
              <p>La seule façon de définir des réponses aux questions de sécurité pour un utilisateur donné est de passer par le portail d’inscription.</p>
            </td>
            <td>
              <p>Les questions de sécurité doivent comporter au maximum 200&#160;caractères et au minimum 3&#160;caractères.</p>
              <p>Les réponses doivent comporter au maximum 40&#160;caractères et au minimum 3&#160;caractères.</p>
            </td>
          </tr>
        </tbody></table>

<br/> <br/> <br/>

**Ressources supplémentaires**


* [Définition de la gestion des mots de passe](active-directory-passwords.md)
* [Fonctionnement de la gestion des mots de passe](active-directory-passwords-how-it-works.md)
* [Prise en main de la gestion des mots de passe](active-directory-passwords-getting-started.md)
* [Personnalisation de la gestion des mots de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion des mots de passe](active-directory-passwords-best-practices.md)
* [Obtention d’informations grâce aux rapports sur la gestion des mots de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion des mots de passe](active-directory-passwords-faq.md)
* [Résolution des problèmes de gestion des mots de passe](active-directory-passwords-troubleshoot.md)
* [Gestion des mots de passe sur MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
 

<!---HONumber=August15_HO6-->