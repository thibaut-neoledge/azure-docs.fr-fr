<properties 
	pageTitle="Personnalisation : gestion des mots de passe Azure AD | Microsoft Azure" 
	description="Comment personnaliser l’apparence, le comportement et les notifications de gestion de mot de passe dans Azure AD en fonction de vos besoins." 
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

# Personnalisation de la gestion des mots de passe en fonction des besoins de votre organisation
Pour offrir la meilleure expérience possible à vos utilisateurs, nous vous recommandons d’explorer et de tester les options de configuration de la gestion de mot de passe qui sont à votre disposition. De fait, vous pouvez de suite commencer cette exploration en ouvrant l’onglet de configuration de l’**extension Active Directory** dans le [portail de gestion Azure](https://manage.windowsazure.com). Cette rubrique vous présente toutes les possibilités de personnalisation de la gestion des mots de passe que vous appliquez en tant qu’administrateur depuis l’onglet **Configurer** de votre annuaire, sur le [portail de gestion Azure](https://manage.windowsazure.com). Par exemple :

- [**Personnalisation de l’interface de gestion des mots de passe**](#password-managment-look-and-feel)
- [**Personnalisation du comportement de gestion des mots de passe des utilisateurs**](#password-management-behavior)
- [**Personnalisation des notifications de gestion des mots de passe**](#password-management-notifications)

## Interface de gestion des mots de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs qui s’inscrivent pour réinitialiser leur mot de passe. Vous pouvez configurer ces options dans la section **Propriétés de l’annuaire** de l’onglet **Configurer**, sur le [portail de gestion Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nom de l’annuaire</p>
              </td>
              <td>
                <p>Détermine le nom que voient les utilisateurs et les administrateurs dans les différentes communications relatives à la réinitialisation du mot de passe.</p>
              </td>
              <td>
                <p>
                  <strong>Messages «&#160;Contactez votre administrateur&#160;»&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine le nom convivial de l’adresse d’expédition, par exemple «&#160;Microsoft, de la part de <strong>Wingtip&#160;Toys</strong>&#160;»<br><br></li>
                  <li class="unordered">
												Détermine l’objet du message, par exemple&#160;: «&#160;Code de vérification du compte <strong>Wingtip Toys</strong>&#160;»<br><br></li>
                </ul>
                <p>
                  <strong>Messages de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine le nom convivial de l’adresse d’expédition, par exemple «&#160;Microsoft, de la part de <strong>Wingtip&#160;Toys</strong>&#160;»<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Apparence de la page de connexion et de la page du panneau d’accès</p>
              </td>
              <td>
                <p>Détermine si les utilisateurs qui accèdent à la page de réinitialisation du mot de passe voient le logo Microsoft ou votre propre logo. Cet élément de configuration ajoute également votre marque au panneau d’accès et à la page de connexion.</p>
                <p>
                  
                </p>
                <p>Pour plus d’informations sur la fonctionnalité de personnalisation et sur l’ajout d’une marque, consultez <a href="https://technet.microsoft.com/library/dn532270.aspx">Ajout d’une marque de société aux pages de connexion et aux pages du panneau d’accès</a>.</p>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine si votre logo apparaît en haut du portail de réinitialisation du mot de passe, à la place du logo Microsoft par défaut.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> il est possible que vous ne voyiez pas votre logo sur la première page du portail de réinitialisation du mot de passe si vous arrivez directement de la page de réinitialisation du mot de passe. Une fois que l’utilisateur entre son ID&#160;utilisateur et clique sur Suivant, votre logo apparaît. Vous pouvez forcer l’affichage du logo lors du chargement de la page avec le paramètre whr pour la page de réinitialisation du mot de passe, comme suit&#160;: <a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a><br><br></li>
                </ul>
                <p>
                  <strong>Messages «&#160;Contactez votre administrateur&#160;»&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine si votre logo apparaît en bas des messages envoyés aux administrateurs lorsque les utilisateurs choisissent de vous contacter en cliquant sur le lien «&#160;Contactez votre administrateur&#160;» dans l’interface de réinitialisation du mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Messages de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine si votre logo apparaît en bas des messages envoyés aux utilisateurs lorsqu’ils réinitialisent leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Comportement de la gestion des mots de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs qui s’inscrivent pour réinitialiser leur mot de passe. Vous pouvez configurer ces options dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** du [portail de gestion Azure](https://manage.windowsazure.com).

> [AZURE.NOTE]Le compte d’administrateur que vous utilisez doit disposer d’une licence AAD Premium pour voir ces contrôles de stratégie.<br><br>Ces contrôles ne s’appliquent qu’aux utilisateurs qui réinitialisent leur mot de passe, et non aux administrateurs. **Les administrateurs ont une stratégie de messagerie et/ou téléphone portable spécifiée par défaut par Microsoft et qui ne peut pas être modifiée.**

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Utilisateurs autorisés à réinitialiser leur mot de passe</p>
              </td>
              <td>
                <p>Détermine si la réinitialisation du mot de passe est activée pour les utilisateurs de cet annuaire. </p>
              </td>
              <td>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est Non, les utilisateurs ne peuvent pas inscrire leurs propres données de vérification.<br><br></li>
                  <li class="unordered">
												Si la valeur est Oui, n’importe quel utilisateur de l’annuaire peut inscrire des données via le portail d’inscription à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> les utilisateurs doivent disposer d’une licence Azure&#160;AD Premium ou Basic avant de pouvoir s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est Non, les utilisateurs voient un message indiquant qu’ils doivent contacter l’administrateur pour réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
												Si la valeur est Oui, les utilisateurs peuvent réinitialiser leur mot de passe automatiquement à l’adresse <a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a>, ou en cliquant sur le lien <strong>Vous ne pouvez pas accéder à votre compte&#160;?</strong> sur la page de connexion de l’organisation.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> les utilisateurs doivent disposer d’une licence Azure&#160;AD Premium ou Basic avant de pouvoir réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Limiter l’accès à la réinitialisation des mots de passe</p>
              </td>
              <td>
                <p>Détermine si seul un groupe d’utilisateurs en particulier est autorisé à utiliser la réinitialisation du mot de passe. (Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est Non, tous les utilisateurs de votre annuaire peuvent s’inscrire à la réinitialisation du mot de passe à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                  <li class="unordered">
												Si la valeur est Oui, seuls les utilisateurs spécifiés dans le contrôle <strong>Groupe pouvant effectuer la réinitialisation des mots de passe</strong> peuvent s’inscrire à la réinitialisation du mot de passe à l’adresse <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a><br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est Non, tous les utilisateurs de votre annuaire peuvent réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
												Si la valeur est Oui, seuls les utilisateurs spécifiés dans le contrôle <strong>Groupe pouvant effectuer la réinitialisation des mots de passe</strong> peuvent réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Groupe pouvant effectuer la réinitialisation des mots de passe</p>
              </td>
              <td>
                <p>Détermine quel groupe d’utilisateurs est autorisé à utiliser la réinitialisation du mot de passe. </p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Limiter l’accès à la réinitialisation des mots de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si aucun groupe n’est spécifié et que vous cliquez sur <strong>Enregistrer</strong>, un groupe vide <strong>SSPRSecurityGroupUsers</strong> est créé pour vous.<br><br></li>
                  <li class="unordered">
												Si vous voulez spécifier votre propre groupe, vous pouvez indiquer votre propre nom d’affichage.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si <strong>Limiter l’accès à la réinitialisation des mots de passe</strong> est défini sur <strong>Oui</strong>, seuls les utilisateurs de ce groupe peuvent s’inscrire à la réinitialisation du mot de passe. <br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si <strong>Limiter l’accès à la réinitialisation des mots de passe</strong> est défini sur <strong>Oui</strong>, seuls les utilisateurs de ce groupe peuvent réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Méthodes d’authentification accessibles aux utilisateurs</p>
              </td>
              <td>
                <p>Détermine les vérifications que les utilisateurs sont autorisés à utiliser pour réinitialiser leur mot de passe.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Vous devez sélectionner au moins une option.<br><br></li>
                  <li class="unordered">
												Nous vous recommandons vivement d’activer au moins deux options pour offrir à vos utilisateurs une plus grande flexibilité lors de la réinitialisation de leur mot de passe.<br><br></li>
                  <li class="unordered">
												Si vous utilisez des questions de sécurité, nous vous recommandons vivement de les utiliser avec une autre méthode d’authentification. Les questions de sécurité peuvent être moins sûres que les méthodes de réinitialisation par téléphone ou par courrier électronique.<br><br></li>
                </ul>
                <p>
                  <strong>Quels sont les champs de l’annuaire qui sont utilisés&#160;?</strong>
                </p>
                <ul>
                  <li class="unordered">
												Téléphone professionnel correspond à l’attribut <strong>Téléphone professionnel</strong> de l’objet utilisateur dans l’annuaire.<br><br></li>
                  <li class="unordered">
												Téléphone mobile correspond à l’attribut <strong>Portable d’authentification</strong> (qui n’est pas public) ou à l’attribut <strong>Téléphone mobile</strong> (qui est public) de l’objet utilisateur dans l’annuaire. Le service vérifie d’abord les données de <strong>Téléphone d’authentification</strong>. Si elles n’existent pas, il revient à l’attribut <strong>Téléphone mobile</strong>.<br><br></li>
                  <li class="unordered">
												L’adresse électronique alternative correspond à l’attribut <strong>Adresse électronique d'authentification</strong> (qui n’est pas public) ou à l’attribut <strong>Adresse de messagerie de secours</strong> (qui est public) de l’objet utilisateur dans l’annuaire. Le service vérifie d’abord les données de <strong>Adresse électronique d’authentification</strong>. Si elles n’existent pas, il revient à l’attribut <strong>Adresse de messagerie de secours</strong>.<br><br></li>
                  <li class="unordered">
												Les questions de sécurité sont stockées de façon privée et sécurisée dans un objet utilisateur dans l’annuaire et ne peuvent être posées qu’aux utilisateurs lors de l’inscription. Pour des raisons de sécurité, il n’existe actuellement aucun moyen pour l’administrateur de modifier ou de voir ces réponses.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> par défaut, seuls les attributs cloud Téléphone professionnel et Téléphone mobile sont synchronisés dans votre annuaire cloud à partir de votre annuaire local. Pour en savoir plus sur les attributs locaux qui sont synchronisés avec le cloud, consultez la page <a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Attributs synchronisés avec Azure&#160;AD</a><br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Affecte les méthodes d’authentification affichées lors de l’inscription des utilisateurs. Si vous n’activez pas une méthode d’authentification donnée, les utilisateurs ne pourront pas s’inscrire automatiquement à cette méthode d’authentification.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> les utilisateurs ne peuvent actuellement pas inscrire leur propre numéro de téléphone professionnel. Cette méthode d’authentification doit être définie par leur administrateur.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine quelles méthodes d’authentification l’utilisateur peut utiliser comme vérifications pour une étape de vérification donnée. Par exemple, si un utilisateur possède à la fois des données dans les champs <strong>Téléphone professionnel</strong> et <strong>Téléphone d’authentification</strong> dans Azure Active Directory, il peut utiliser une de ces méthodes d’authentification pour récupérer son mot de passe.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> les utilisateurs ne peuvent réinitialiser leur mot de passe que si et seulement s’ils ont des données présentes dans les méthodes d’authentification que vous avez activées en tant qu’administrateur.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nombre de méthodes d’authentification requises</p>
              </td>
              <td>
                <p>Détermine le nombre minimal de méthodes d’authentification disponibles que l’utilisateur doit appliquer pour réinitialiser son mot de passe.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Peut être défini sur 1 ou 2 méthodes d’authentification.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine le nombre minimal de méthodes d’authentification auxquelles l’utilisateur doit s’inscrire avant de pouvoir terminer l’inscription.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Affecte le nombre d’étapes de vérification par lesquelles doit passer l’utilisateur avant de pouvoir réinitialiser son mot de passe. Une étape de vérification est une procédure dans laquelle l’utilisateur recourt à une information d’authentification (un appel à son téléphone professionnel ou un courrier électronique à son adresse de messagerie de secours) pour vérifier son compte.<br><br></li>
                  <li class="unordered">
                    <strong>Remarque&#160;:</strong> si l’utilisateur ne dispose pas de suffisamment d’informations d’authentification définies sur son compte pour que la réinitialisation de son mot de passe réussisse conformément à la stratégie que vous avez définie, il verra s’afficher une page d’erreur lui demandant de contacter un administrateur pour réinitialiser son mot de passe.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nombre de questions requises pour l’inscription</p>
              </td>
              <td>
                <p>Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre lors de l’inscription pour l’option des questions de sécurité.</p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Il est possible de définir de 3 à 5 questions pour l’inscription.<br><br></li>
                  <li class="unordered">
												Le nombre de questions requises pour l’inscription doit être supérieur ou égal au nombre de questions requises pour la réinitialisation.<br><br></li>
                  <li class="unordered">
												Nous vous recommandons de définir un nombre de questions pour l’inscription supérieur au nombre de questions requises pour la réinitialisation afin que les utilisateurs bénéficient de plus de souplesse lors de la réinitialisation de leur mot de passe. Il s’agit également d’une configuration plus sécurisée, les questions sont sélectionnées au hasard dans toutes les questions qu’ils ont inscrites.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre avant qu’il ne soit considéré comme entièrement inscrit à la réinitialisation du mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nombre de questions requises pour la réinitialisation </p>
              </td>
              <td>
                <p>Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre lors de la réinitialisation du mot de passe.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Il est possible de définir de 3 à 5 questions pour la réinitialisation.<br><br></li>
                  <li class="unordered">
												Le nombre de questions requises pour la réinitialisation doit être inférieur ou égal au nombre de questions requises pour l’inscription.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine le nombre minimal de questions auxquelles un utilisateur doit répondre avant que son mot de passe ne puisse être réinitialisé.<br><br></li>
                  <li class="unordered">
												Au moment de la réinitialisation du mot de passe, ce nombre de questions est sélectionné au hasard dans la liste des questions inscrites par l’utilisateur. Par exemple, si l’utilisateur a inscrit 5&#160;questions, 3 de ces 5 questions sont sélectionnées au hasard lorsque l’utilisateur demande à réinitialiser son mot de passe. L’utilisateur doit ensuite répondre à toutes ces questions correctement avant que le mot de passe ne puisse être réinitialisé.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Question de sécurité</p>
              </td>
              <td>
                <p>Définit les questions de sécurité que vos utilisateurs peuvent choisir lors de l’inscription à la réinitialisation du mot de passe et lors de la réinitialisation de leur mot de passe.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si la case à cocher <strong>Questions de sécurité</strong> est activée).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Il est possible de définir jusqu’à 20&#160;questions.<br><br></li>
                  <li class="unordered">
												La limite maximale du nombre de caractères par question est de 200&#160;caractères.<br><br></li>
                  <li class="unordered">
												La limite minimale du nombre de caractères par question est de 3&#160;caractères.<br><br></li>
                  <li class="unordered">
												La limite maximale du nombre de caractères par réponse est de 40&#160;caractères.<br><br></li>
                  <li class="unordered">
												L’utilisateur ne peut pas répondre deux fois à la même question.<br><br></li>
                  <li class="unordered">
												L’utilisateur ne peut pas fournir la même réponse à deux questions différentes.<br><br></li>
                  <li class="unordered">
												N’importe quel jeu de caractères peut être utilisé pour définir les questions et les réponses (y compris les caractères Unicode).<br><br></li>
                  <li class="unordered">
												Le nombre de questions définies doit être supérieur ou égal au nombre de questions requises pour l’inscription.<br><br></li>
                  <li class="unordered">
												Il n’est pas encore possible de définir différentes questions en fonction des paramètres régionaux, mais cela sera possible à l’avenir.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine les questions auxquelles l’utilisateur est en mesure de répondre lors de l’inscription pour la réinitialisation du mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Détermine les questions que l’utilisateur peut utiliser pour réinitialiser son mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au panneau d’accès&#160;?</p>
                <p>
                  
                </p>
              </td>
              <td>
                <p>Détermine si les utilisateurs doivent fournir leurs coordonnées pour la réinitialisation du mot de passe la prochaine fois qu’ils se connecteront au panneau d’accès.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si vous désactivez cette fonctionnalité, vous pouvez également envoyer manuellement les utilisateurs vers <a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> pour qu’ils inscrivent leurs coordonnées.  <br><br></li>
                  <li class="unordered">
												Les utilisateurs peuvent accéder au portail d’inscription en cliquant sur le lien <strong>Inscription pour réinitialiser leur mot de passe</strong> sous l’onglet de profil du panneau d’accès.<br><br></li>
                  <li class="unordered">
												L’inscription à l’aide de cette méthode peut être désactivée en cliquant sur le bouton Annuler ou en fermant la fenêtre, mais les utilisateurs recevront un rappel à chaque connexion s’ils ne se sont pas inscrits.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ce paramètre n’affecte pas le comportement du portail d’inscription, mais détermine si ce dernier est présenté aux utilisateurs lorsqu’ils se connectent au panneau d’accès.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Nombre de jours avant que les utilisateurs ne doivent confirmer leurs coordonnées</p>
              </td>
              <td>
                <p>Lorsque <strong>Demander aux utilisateurs de s’inscrire</strong> est activé, ce paramètre détermine le temps qui peut s’écouler avant que l’utilisateur ne doive reconfirmer ses coordonnées. </p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au panneau d’accès</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  
                </p>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Les valeurs acceptées vont de 0 à 730 jours, 0 voulant dire que l’utilisateur ne devra jamais reconfirmer ses coordonnées.<br><br></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ce paramètre n’affecte pas le comportement du portail d’inscription, mais détermine si le portail d’inscription est présenté aux utilisateurs lorsque leurs coordonnées doivent être reconfirmées. <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Personnaliser le lien «&#160;Contactez votre administrateur&#160;»</p>
              </td>
              <td>
                <p>Contrôle si le lien Contactez votre administrateur (illustré à gauche) apparaît sur le portail de réinitialisation du mot de passe lorsqu’une erreur se produit, lorsque l’utilisateur attend trop longtemps ou lorsqu’une opération pointe vers une URL ou une adresse électronique personnalisée.</p>
                <p>
                  
                </p>
                <p>(Visible uniquement si <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si vous activez ce paramètre, vous devez choisir une adresse URL ou une adresse électronique personnalisée en remplissant le champ <strong>Adresse de messagerie ou URL personnalisée </strong> situé en dessous.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est Non, si l’utilisateur clique sur le lien en surbrillance, un message est envoyé à l’adresse électronique principale de tous les administrateurs de ce client, demandant que son mot de passe soit réinitialisé. Ce message est envoyé selon la logique suivante&#160;:<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
														S’ils existent, le message est envoyé à tous les administrateurs de mot de passe, avec un maximum de 100&#160;destinataires au total.<br><br></li>
                      <li class="unordered">
														S’il n’y a pas d’administrateurs de mot de passe, le message est envoyé à tous les administrateurs d’utilisateurs, avec un maximum de 100&#160;destinataires au total.<br><br></li>
                      <li class="unordered">
														S’il n’y a pas d’administrateurs d’utilisateurs, le message est envoyé à tous les administrateurs généraux, avec un maximum de 100&#160;destinataires au total.<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
												Si la valeur est Oui, ce paramètre personnalise le comportement du lien mis en surbrillance pour qu’il pointe vers une URL ou une adresse électronique personnalisée via laquelle vos utilisateurs peuvent obtenir une aide sur la réinitialisation du mot de passe.<br><br></li>
                  <li class="unordered">
												Si vous spécifiez une URL, elle s’ouvre dans un nouvel onglet.<br><br></li>
                  <li class="unordered">
												Si vous spécifiez une adresse électronique, un lien mailto vers cette adresse est créé.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Adresse électronique ou URL personnalisée</p>
              </td>
              <td>
                <p>Contrôle l’adresse électronique ou l’URL à laquelle renvoie le lien <strong>Contactez votre administrateur</strong>. </p>
                <p>
                  
                </p>
                <p>(Visible uniquement si l’option <strong>Personnaliser le lien «&#160;Contactez votre administrateur&#160;»</strong> est défini sur <strong>Oui</strong>).</p>
              </td>
              <td>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Il doit s’agir d’une URL d’intranet ou d’extranet valide ou d’une adresse électronique valide.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Modifie l’emplacement sur lequel pointe le lien <strong>Contactez votre administrateur</strong>.<br><br></li>
                  <li class="unordered">
												Si vous fournissez une adresse électronique, le lien devient un lien «&#160;mailto&#160;» vers cette adresse.<br><br></li>
                  <li class="unordered">
												Si vous fournissez une URL, le lien devient un lien href standard pointant vers cette URL, qui s’ouvre dans un nouvel onglet.  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Écriture différée des mots de passe dans l’annuaire local</p>
              </td>
              <td>
                <p>Contrôle si l’écriture différée de mot de passe est activée ou non pour cet annuaire. Si elle l’est, indique l’état du service d’écriture différée local.</p>
                <p>
                  
                </p>
                <p>C’est utile si vous voulez désactiver temporairement le service en cas de panne.</p>
              </td>
              <td>
                <p>
                  
                </p>
                <p>
                  <strong>Remarque&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Ce contrôle apparaît uniquement si vous avez installé l’écriture différée du mot de passe en téléchargeant la dernière version d’Azure AD Connect et activé l’option <strong>Écriture différée de mot de passe</strong> dans l’écran de sélection des <strong>fonctionnalités facultatives</strong>.<br><br></li>
                  <li class="unordered">
												Si vous avez activé l’écriture différée du mot de passe et que vous pensez que le service présente un problème de configuration, vous pouvez ouvrir cet onglet et consulter l’étiquette <strong>État du service d’écriture en différé des mots de passe</strong> pour voir si quelque chose ne fonctionne pas.<br><br></li>
                  <li class="unordered">
												Les états possibles sont&#160;:<br><br><ul><li class="unordered"><strong>Configuré</strong>&#160;: tout fonctionne comme prévu<br><br></li><li class="unordered"><strong>Non configuré</strong>&#160;: l’écriture différée du mot de passe est installée, mais le service est injoignable. Vérifiez si vous ne bloquez pas les connexions sortantes sur le port&#160;443 et essayez de réinstaller le service si les problèmes persistent.<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>Portail d’inscription&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si l’écriture différée du mot de passe est déployée et configurée et que ce commutateur est défini sur <strong>Non</strong>, l’écriture différée est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne pourront pas s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
												Si le commutateur est défini sur <strong>Oui</strong>, l’écriture différée est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe pourront s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si l’écriture différée du mot de passe est déployée et configurée et que ce commutateur est défini sur <strong>Non</strong>, l’écriture différée est désactivée et les utilisateurs fédérés et synchronisés par hachage du mot de passe ne pourront pas réinitialiser leur mot de passe.<br><br></li>
                  <li class="unordered">
												Si le commutateur est défini sur <strong>Oui</strong>, l’écriture différée est activée et les utilisateurs fédérés et synchronisés par hachage du mot de passe pourront s’inscrire pour réinitialiser leur mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## Notifications de gestion du mot de passe
Le tableau suivant décrit comment chaque contrôle affecte l’expérience des utilisateurs et des administrateurs qui reçoivent les notifications de réinitialisation des mots de passe. Vous pouvez configurer ces options dans la section **Notifications** de l’onglet **Configurer** du [portail de gestion Azure](https://manage.windowsazure.com).

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>Contrôle de stratégie</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Description</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>Éléments affectés</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <p>Avertir les administrateurs quand d’autres administrateurs réinitialisent leur mot de passe</p>
              </td>
              <td>
                <p>Détermine si tous les généraux sont informés par courrier électronique à leur adresse électronique principale lorsqu’un autre administrateur de n’importe quel type réinitialise son mot de passe.</p>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est définie sur Non, aucune notification n’est envoyée.<br><br></li>
                  <li class="unordered">
												Si la valeur est définie sur Oui, tous les autres administrateurs généraux sont avertis si un administrateur réinitialise son mot de passe.<br><br></li>
                  <li class="unordered">
												Cette notification est envoyée via un courrier électronique à l’adresse électronique principale de tous les autres administrateurs généraux de l’organisation.<br><br></li>
                </ul>
                <p>
                  <strong>Exemple&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si cette option est activée quand l’administrateur&#160;A réinitialise son mot de passe et qu’il existe trois autres administrateurs, B, C et D, les administrateurs B, C et D reçoivent un message les informant de la réinitialisation du mot de passe de l’administrateur&#160;A.<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <p>Avertir les utilisateurs et les administrateurs quand leur mot de passe a été réinitialisé</p>
              </td>
              <td>
                <p>Détermine si les utilisateurs et les administrateurs qui ont réinitialisé leur mot de passe doivent recevoir une notification les informant que leur mot de passe a été réinitialisé.</p>
              </td>
              <td>
                <p>
                  <strong>Portail de réinitialisation du mot de passe&#160;:</strong>
                </p>
                <ul>
                  <li class="unordered">
												Si la valeur est définie sur Non, aucune notification n’est envoyée.<br><br></li>
                  <li class="unordered">
												Si la valeur est définie sur Oui, chaque fois qu’un utilisateur ou qu’un administrateur réinitialise son propre mot de passe, il reçoit une notification indiquant que son mot de passe a été réinitialisé.<br><br></li>
                  <li class="unordered">
												Cette notification est envoyée via un courrier électronique à l’adresse principale et à l’autre adresse électronique (ou adresse électronique d’authentification) de l’utilisateur qui réinitialise son mot de passe.<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/> <br/> <br/>

**Ressources supplémentaires**


* [Définition de la gestion des mots de passe](active-directory-passwords.md)
* [Fonctionnement de la gestion des mots de passe](active-directory-passwords-how-it-works.md)
* [Prise en main de la gestion des mots de passe](active-directory-passwords-getting-started.md)
* [Meilleures pratiques de gestion des mots de passe](active-directory-passwords-best-practices.md)
* [Obtention d’informations grâce aux rapports sur la gestion des mots de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion des mots de passe](active-directory-passwords-faq.md)
* [Résolution des problèmes de gestion des mots de passe](active-directory-passwords-troubleshoot.md)
* [En savoir plus](active-directory-passwords-learn-more.md)
* [Gestion des mots de passe sur MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx) 

<!---HONumber=August15_HO6-->