<properties 
	pageTitle="Résolution des problèmes : Gestion des mots de passe Azure AD | Microsoft Azure" 
	description="Étapes courantes de résolution des problèmes de la gestion des mots de passe Azure AD, y compris la réinitialisation, la modification, l’écriture différée, l’inscription et les informations à inclure lorsque vous recherchez de l’aide." 
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
	ms.date="11/16/2015" 
	ms.author="asteen"/>

# Résolution des problèmes de gestion des mots de passe
Si vous rencontrez des problèmes de gestion des mots de passe, nous sommes ici pour vous aider. La plupart des problèmes que vous pouvez rencontrer peuvent être résolus par quelques étapes simples de dépannage que vous trouverez ci-dessous pour résoudre les problèmes de votre déploiement :

* [**Informations à inclure lorsque vous avez besoin d’aide**](#information-to-include-when-you-need-help)
* [**Problèmes de configuration de la gestion des mots de passe dans le portail de gestion Azure**](#troubleshoot-password-reset-configuration-in-the-azure-management-portal)
* [**Problèmes des rapports de la gestion des mots de passe dans le portail de gestion Azure**](#troubleshoot-password-management-reports-in-the-azure-management-portal)
* [**Problèmes du portail d’inscription à la réinitialisation du mot de passe**](#troubleshoot-the-password-reset-registration-portal)
* [**Problèmes du portail de réinitialisation du mot de passe**](#troubleshoot-the-password-reset-portal)
* [**Problèmes d’écriture différée de mot de passe**](#troubleshoot-password-writeback)
  - [Codes d’erreur du journal des événements Écriture différée de mot de passe](#password-writeback-event-log-error-codes)
  - [Problèmes de connectivité de l’écriture différée de mot de passe](#troubleshoot-password-writeback-connectivity)

Si vous avez essayé les étapes de dépannage décrites ci-dessous et que vous rencontrez toujours des problèmes, vous pouvez publier une question sur les [Forums d’Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou contacter le support technique afin que nous puissions résoudre votre problème dès que possible.

## Informations à inclure lorsque vous avez besoin d’aide

Si vous ne pouvez pas résoudre le problème avec les conseils ci-dessous, vous pouvez contacter nos ingénieurs de support. Lorsque vous les contactez, il est recommandé de fournir les informations suivantes :

 - **Description générale de l’erreur** : quel message d’erreur exact est apparu ? Si aucun message d’erreur n’est apparu, décrivez en détail le comportement inattendu remarqué.
 - **Page** : sur quelle page vous trouviez-vous lorsque l’erreur est apparue (incluez l’URL) ?
 - **Date/heure/fuseau horaire** : quelles étaient la date et l’heure auxquelles l’erreur s’est produite (incluez le fuseau horaire) ?
 - **Code de support** : quel était le code de support généré lorsque l’erreur s’est produite (pour le trouver, reproduisez l’erreur, puis cliquez sur le lien Code de support en bas de l’écran et envoyez à l’ingénieur du support technique le GUID obtenu). 
   - Si vous êtes dans une page sans code de support dans la partie inférieure, appuyez sur F12 et recherchez le SID et le CID et envoyez ces deux résultats à l’ingénieur de support.

    ![][001]

 - **ID utilisateur** : quel est l’ID de l’utilisateur qui a vu l’erreur (par exemple user@contoso.com)?)
 - **Informations sur l’utilisateur** : l’utilisateur était-il fédéré, disposait-il de la synchronisation du hachage de mot de passe ou était-ce un utilisateur du cloud uniquement ? L’utilisateur dispose-t-il d’une licence AAD Premium ou AAD Basic ?
 - **Journal des événements de l’application** : si vous utilisez l’écriture différée de mot de passe et que l’erreur se produit dans votre infrastructure locale, veuillez compresser une copie du journal des événements de l’application provenant de votre serveur Azure AD Connect et l’envoyer avec votre demande.

Ces informations nous aideront à résoudre votre problème aussi rapidement que possible.


## Résolution des problèmes de configuration de réinitialisation du mot de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lors de la configuration de la réinitialisation du mot de passe, vous pourrez peut-être la résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erreur qui s’affiche</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas la section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> sous l’onglet <strong>Configurer</strong> du portail de gestion Azure.</p>
            </td>
            <td>
              <p>La section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> n’est pas visible dans l’onglet <strong>Configurer</strong> du portail de gestion Azure.</p>
            </td>
            <td>
              <p>Cela peut se produire si l’administrateur effectuant cette opération ne dispose pas d’une licence AAD Premium ou AAD Basic. </p>
              <p>Pour résoudre ce problème, attribuez une licence AAD Premium ou AAD Basic au compte d’administrateur en question en accédant à l’onglet <strong>Licences</strong> et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas les options de configuration dans la section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> qui sont décrites dans la documentation.</p>
            </td>
            <td>
              <p>La section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> est visible mais le seul indicateur qui apparaît dans cette section est l’indicateur <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong>.</p>
            </td>
            <td>
              <p>Le reste de l’interface utilisateur s’affiche lorsque vous basculez l’indicateur <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> sur <strong>Oui</strong>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas d’option de configuration spécifique.</p>
            </td>
            <td>
              <p>Par exemple, je ne vois pas l’option <strong>Nombre de jours avant lequel un utilisateur doit confirmer ses données de contact</strong> lorsque je fais défiler la section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> (ou autres exemples du même problème).</p>
            </td>
            <td>
              <p>De nombreux éléments de l’interface utilisateur sont masqués jusqu’à ce qu’ils soient nécessaires. Essayez d’activer toutes les options de la page pour les afficher.</p>
              <p>Consultez la rubrique <a href="../active-directory-passwords-customize#password-management-behavior">Personnalisation de la stratégie de réinitialisation du mot de passe utilisateur</a> pour plus d’informations sur tous les contrôles qui sont à votre disposition.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois pas l’option de configuration <strong>Écriture différée des mots de passe dans l’annuaire local</strong>.</p>
            </td>
            <td>
              <p>L’option <strong>Écriture différée des mots de passe dans l’annuaire local</strong> n’est pas visible dans l’onglet <strong>Configurer</strong> du portail de gestion Azure.</p>
            </td>
            <td>
              <p>Cette option est visible uniquement si vous avez téléchargé Azure AD Connect et configuré l’écriture différée de mot de passe. Après cela, cette option apparaît et vous permet d’activer ou de désactiver l’écriture différée depuis le cloud.</p>
              <p>Consultez la rubrique <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Activation ou désactivation de l’écriture différée de mot de passe</a> pour plus d’informations sur la procédure à suivre.</p>
            </td>
          </tr>
        </tbody></table>

## Résolution des problèmes de rapports de la gestion des mots de passe dans le portail de gestion Azure
Si vous rencontrez une erreur lors de l’utilisation des rapports de la gestion des mots de passe, vous pourrez peut-être la résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erreur qui s’affiche</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Je ne vois aucun rapport de gestion des mots de passe.</p>
            </td>
            <td>
              <p>Les rapports <strong>Activité de réinitialisation de mot de passe</strong> et <strong>Activité d’inscription de réinitialisation de mot de passe</strong> ne sont pas visibles dans les rapports <strong>Journal d’activité</strong> de l’onglet <strong>Rapports</strong>.</p>
            </td>
            <td>
              <p>Cela peut se produire si l’administrateur effectuant cette opération ne dispose pas d’une licence AAD Premium ou AAD Basic. </p>
              <p>Pour résoudre ce problème, attribuez une licence AAD Premium ou AAD Basic au compte d’administrateur en question en accédant à l’onglet <strong>Licences</strong> et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Les inscriptions de l’utilisateur apparaissent plusieurs fois.</p>
            </td>
            <td>
              <p>Quand un utilisateur inscrit une adresse de messagerie, un téléphone portable et des questions de sécurité secondaires, ils apparaissent sur plusieurs lignes distinctes au lieu d’une seule ligne.</p>
            </td>
            <td>
              <p>Actuellement, quand un utilisateur s’inscrit, nous ne pouvons pas savoir s’il va enregistrer tout ce qui se trouve dans la page d’inscription. Par conséquent, nous journalisons actuellement chaque élément de données individuel sous la forme d’un événement distinct.</p>
              <p>Si vous souhaitez agréger ces données, vous pouvez télécharger le rapport et ouvrir les données dans un tableau croisé dynamique dans Excel pour plus de flexibilité.</p>
            </td>
          </tr>
        </tbody></table>

## Résolution des problèmes du portail d’inscription à la réinitialisation de mot de passe
Si vous rencontrez une erreur lors de l’inscription d’un utilisateur à la réinitialisation du mot de passe, vous pourrez peut-être la résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erreur qui s’affiche</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’annuaire n’est pas activé pour la réinitialisation du mot de passe.</p>
            </td>
            <td>
              <p>Votre administrateur ne vous a pas autorisé à utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Faites basculer l’indicateur <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> sur <strong>Oui</strong> et cliquez sur <strong>Enregistrer</strong> sous l’onglet Configuration de l’annuaire du portail de gestion Azure. L’administrateur effectuant cette opération doit disposer d’une licence Azure AD Premium ou Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur ne dispose pas d’une licence AAD Premium ou AAD Basic.</p>
            </td>
            <td>
              <p>Votre administrateur ne vous a pas autorisé à utiliser cette fonctionnalité.</p>
            </td>
            <td>
              <p>Attribuez une licence Azure AD Premium ou Azure AD Basic à l’utilisateur sous l’onglet <strong>Licences</strong> du portail de gestion Azure. L’administrateur effectuant cette opération doit disposer d’une licence Azure AD Premium ou Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>L’utilisateur voit une erreur indiquant&#160;:</p>
              <p>
                
              </p>
              <p>Erreur de traitement de la demande </p>
              <p>quand vous essayez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais en général, cette erreur est due à une panne ou à un problème de configuration du service qui ne peuvent pas être résolus. </p>
              <p>Si cette erreur se produit et qu’elle a un impact sur votre entreprise, contactez le support technique pour que nous vous aidions dès que possible. Consultez la rubrique <a href="#information-to-include-when-you-need-help">Informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir à l’ingénieur du support technique pour qu’il assure une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## Résolution des problèmes du portail de réinitialisation du mot de passe
Si vous rencontrez une erreur lors de la réinitialisation d’un mot de passe pour un utilisateur, vous pourrez peut-être la résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erreur qui s’affiche</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’annuaire n’est pas activé pour la réinitialisation du mot de passe.</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour la réinitialisation du mot de passe</p>
              <p>Désolé, votre administrateur n’a pas configuré votre compte pour ce service. </p>
              <p>
                
              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place.</p>
            </td>
            <td>
              <p>Faites basculer l’indicateur <strong>Utilisateurs autorisés à réinitialiser leur mot de passe</strong> sur <strong>Oui</strong> et cliquez sur <strong>Enregistrer</strong> sous l’onglet Configuration de l’annuaire du portail de gestion Azure. L’administrateur effectuant cette opération doit disposer d’une licence Azure AD Premium ou Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur ne dispose pas d’une licence AAD Premium ou AAD Basic.</p>
            </td>
            <td>
              <p>Bien que nous ne puissions pas réinitialiser automatiquement les mots de passe de compte non-administrateur, nous pouvons contacter l’administrateur de votre organisation pour qu’il le fasse à votre place.</p>
            </td>
            <td>
              <p>Attribuez une licence Azure AD Premium ou Azure AD Basic à l’utilisateur sous l’onglet <strong>Licences</strong> du portail de gestion Azure. L’administrateur effectuant cette opération doit disposer d’une licence Azure AD Premium ou Basic.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’annuaire est activé pour la réinitialisation du mot de passe, mais il manque une information d’authentification ou celle-ci n’est pas bien formée.</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour la réinitialisation du mot de passe</p>
              <p>Désolé, votre administrateur n’a pas configuré votre compte pour ce service. </p>
              <p>
                
              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place.</p>
            </td>
            <td>
              <p>Assurez-vous que l’utilisateur dispose de données de contact correctes dans l’annuaire avant de poursuivre. Consultez la rubrique <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Données utilisées par la réinitialisation de mot de passe</a> pour plus d’informations sur la configuration des informations d’authentification dans l’annuaire afin que les utilisateurs ne voient pas cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’annuaire est activé pour la réinitialisation du mot de passe. Toutefois, l’utilisateur n’a fourni qu’une seule partie des données de contact alors que la stratégie nécessite deux étapes de vérification.</p>
            </td>
            <td>
              <p>Votre compte n’est pas activé pour la réinitialisation du mot de passe</p>
              <p>Désolé, votre administrateur n’a pas configuré votre compte pour ce service. </p>
              <p>
                
              </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place.</p>
            </td>
            <td>
              <p>Avant de continuer, vérifiez que l’utilisateur a configuré correctement au moins deux méthodes de contact (par exemple, un téléphone mobile et un téléphone fixe). Consultez la rubrique <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Données utilisées par la réinitialisation de mot de passe</a> pour plus d’informations sur la configuration des informations d’authentification dans l’annuaire afin que les utilisateurs ne voient pas cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’annuaire est activé pour la réinitialisation du mot de passe et l’utilisateur est correctement configuré, mais l’utilisateur est injoignable. </p>
            </td>
            <td>
              <p>Désolé, nous avons rencontré une erreur inattendue lors de notre tentative de contact.</p>
            </td>
            <td>
              <p>Cela peut être dû à une erreur de service temporaire ou à des données de contact incorrectement configurées qui n’ont pas été signalées comme telles. Si l’utilisateur patiente pendant 10 secondes, un lien l’invitant à réessayer et à contacter son administrateur apparaît. S’il clique sur Réessayer, l’appel est redistribué. En revanche, s’il clique sur « Contacter votre administrateur », un formulaire est envoyé par courrier électronique aux administrateurs d’utilisateurs, administrateurs de mots de passe et administrateurs généraux (dans cet ordre) pour leur demander de réinitialiser le mot de passe pour ce compte d’utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur ne reçoit jamais l’appel téléphonique ou le SMS de réinitialisation du mot de passe.</p>
            </td>
            <td>
              <p>L’utilisateur clique sur le lien pour recevoir un SMS ou être appelé, mais ne reçoit jamais rien.</p>
            </td>
            <td>
              <p>Cela peut être dû à un numéro de téléphone mal formé dans l’annuaire. Assurez-vous que le numéro de téléphone est au format «&#160;+ccc xxxyyyzzzzXeeee&#160;». Pour en savoir plus sur le format des numéros de téléphone à utiliser avec la réinitialisation des mots de passe, consultez la rubrique <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Données utilisées par la réinitialisation de mot de passe</a>.</p>
              <p>Si vous avez besoin d’orienter l’utilisateur vers un poste particulier, notez que la réinitialisation de mot de passe ne prend pas en charge les numéros de poste, même si vous en spécifiez un dans l’annuaire (ils sont supprimés avant la distribution de l’appel). Essayez d’utiliser un nombre sans numéro de poste ou d’intégrer le numéro de poste dans le numéro de téléphone dans votre PBX.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur ne reçoit jamais le message électronique de réinitialisation du mot de passe.</p>
            </td>
            <td>
              <p>L’utilisateur clique sur le lien pour recevoir un message électronique, puis ne reçoit jamais rien.</p>
            </td>
            <td>
              <p>La cause la plus probable de ce problème est que le message est rejeté par un filtre antispam. Consultez votre dossier de courrier indésirable ou d’éléments supprimés pour y rechercher le message électronique.</p>
              <p>Vérifiez également que vous êtes bien en train de vérifier la bonne adresse&#160;: beaucoup de gens ont des adresses de messagerie très similaires et se trompent de boîte de réception. Si aucune de ces options ne fonctionne, il est également possible que l’adresse de messagerie soit mal formée dans l’annuaire. Vérifiez que l’adresse de messagerie est correcte et réessayez. Pour en savoir plus sur le format des adresses de messagerie à utiliser avec la réinitialisation des mots de passe, consultez la rubrique <a href="../active-directory-passwords-learn-more#what-data-is-used-by-password-reset">Données utilisées par la réinitialisation de mot de passe</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>J’ai défini une stratégie de réinitialisation du mot de passe, mais quand un compte d’administrateur utilise la réinitialisation du mot de passe, cette stratégie n’est pas appliquée.</p>
            </td>
            <td>
              <p>Les comptes d’administrateur qui réinitialisent leurs mots de passe voient les mêmes options activées pour la réinitialisation de mot de passe, l’adresse de messagerie et le téléphone mobile, quelle que soit la stratégie définie dans la section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> de l’onglet <strong>Configurer</strong>.</p>
            </td>
            <td>
              <p>Les options configurées sous la section <strong>Stratégie de réinitialisation du mot de passe utilisateur</strong> de l’onglet <strong>Configurer</strong> s’appliquent uniquement aux utilisateurs finaux de votre organisation.</p>
              <p>Microsoft gère et contrôle la stratégie de réinitialisation du mot de passe administrateur afin de garantir le plus haut niveau de sécurité possible.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur est empêché de tenter de réinitialiser son mot de passe un nombre de fois par jour trop élevé.</p>
            </td>
            <td>
              <p>L’utilisateur voit une erreur indiquant&#160;:</p>
              <p>
                
              </p>
              <p>Veuillez utiliser une autre option.</p>
              <p>Vous avez tenté de vérifier votre compte un trop grand nombre de fois au cours de la dernière heure. Pour des raisons de sécurité, vous devez attendre 24&#160;heures avant de réessayer. </p>
              <p>Si vous le souhaitez, nous pouvons contacter un administrateur de votre organisation pour réinitialiser le mot de passe à votre place.</p>
            </td>
            <td>
              <p>Nous implémentons un mécanisme de limitation automatique pour empêcher les utilisateurs d’essayer de réinitialiser leurs mots de passe un nombre de fois trop élevé sur un court laps de temps. Cette limitation se produit quand&#160;:</p>
              <ol class="ordered">
                <li>
										L’utilisateur tente de valider un numéro de téléphone 5&#160;fois en une heure.<br\><br\></li>
                <li>
										L’utilisateur tente d’utiliser la méthode des questions de sécurité 5&#160;fois en une heure.<br\><br\></li>
                <li>
										L’utilisateur tente de réinitialiser un mot de passe pour le même compte d’utilisateur 5&#160;fois en une heure.<br\><br\></li>
              </ol>
              <p>Pour résoudre ce problème, demandez à l’utilisateur d’attendre 24 heures après la dernière tentative. Il pourra alors réinitialiser son mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’utilisateur rencontre une erreur lors de la validation de son numéro de téléphone.</p>
            </td>
            <td>
              <p>Quand l’utilisateur essaie de vérifier un téléphone à utiliser comme méthode d’authentification, il voit une erreur indiquant&#160;:</p>
              <p>
                
              </p>
              <p>Le numéro de téléphone spécifié est incorrect.</p>
            </td>
            <td>
              <p>Cette erreur se produit quand le numéro de téléphone entré ne correspond pas au numéro de téléphone enregistré.</p>
              <p>Assurez-vous que l’utilisateur entre le numéro de téléphone complet, y compris le code de zone et de pays, quand il essaie d’utiliser une méthode par téléphone pour la réinitialisation du mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de traitement de la demande</p>
            </td>
            <td>
              <p>L’utilisateur voit une erreur indiquant&#160;:</p>
              <p>
                
              </p>
              <p>Erreur de traitement de la demande </p>
              <p>quand vous essayez de réinitialiser un mot de passe.</p>
            </td>
            <td>
              <p>Cela peut être dû à de nombreux problèmes, mais en général, cette erreur est due à une panne ou à un problème de configuration du service qui ne peuvent pas être résolus. </p>
              <p>Si cette erreur se produit et qu’elle a un impact sur votre entreprise, contactez le support technique pour que nous vous aidions dès que possible. Consultez la rubrique <a href="#information-to-include-when-you-need-help">Informations à inclure lorsque vous avez besoin d’aide</a> pour voir ce que vous devez fournir à l’ingénieur du support technique pour qu’il assure une résolution rapide.</p>
            </td>
          </tr>
        </tbody></table>

## Résolution des problèmes d’écriture différée de mot de passe
Si vous rencontrez une erreur lors de l’activation, la désactivation ou l’utilisation de l’écriture différée de mot de passe, vous pourrez peut-être la résoudre en suivant les étapes de dépannage ci-dessous :

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Cas d’erreur</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Erreur qui s’affiche</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Solution</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échecs lors de l’intégration générale et du démarrage</p>
            </td>
            <td>
              <p>Le service de réinitialisation du mot de passe ne démarre pas localement avec une erreur 6800 dans le journal des événements de l’application de l’ordinateur Azure AD Connect.</p>
              <p>
                
              </p>
              <p>Après l’intégration, les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe ne parviennent pas à réinitialiser leurs mots de passe.</p>
            </td>
            <td>
              <p>Quand l’écriture différée des mots de passe est activée, le moteur de synchronisation appelle la bibliothèque d’écriture différée pour effectuer la configuration (intégration) en contactant le service d’intégration cloud. Toutes les erreurs rencontrées au cours de l’intégration ou lors du démarrage du point de terminaison WCF pour l’écriture différée des mots de passe entraînent une consignation dans le journal des événements de votre ordinateur Azure AD Connect.</p>
              <p>Pendant le redémarrage du service ADSync, si l’écriture différée a été configurée, le point de terminaison WCF est démarré. Toutefois, si le démarrage du point de terminaison échoue, l’événement&#160;6800 est tout simplement consigné et le service de synchronisation peut démarrer. La présence de cet événement signifie que le point de terminaison de l’écriture différée de mot de passe n’a pas démarré. Les détails du journal des événements pour cet événement (6800) ainsi que les entrées du journal des événements générées par le composant PasswordResetService indiquent pour quelles raisons le point de terminaison n’a pas pu démarrer. Passez en revue les erreurs du journal des événements et essayez de redémarrer Azure AD Connect si l’écriture différée de mot de passe ne fonctionne toujours pas. Si le problème persiste, essayez de désactiver et de réactiver l’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de configuration de l’écriture différée lors de l’installation d’Azure AD Connect.</p>
            </td>
            <td>
              <p>À la dernière étape du processus d’installation d’Azure AD Connect, une erreur indique que l’écriture différée de mot de passe n’a pas pu être configurée.</p>
              <p>
                
              </p>
              <p>Le journal des événements de l’application Azure AD Connect contient l’erreur&#160;32009 avec le texte «&#160;Erreur lors de l’obtention du jeton d’authentification&#160;».</p>
            </td>
            <td>
              <p>Cette erreur se produit dans les deux cas suivants&#160;:</p>
              <ul>
                <li class="unordered">
										Vous avez spécifié un mot de passe incorrect pour le compte d’administrateur général spécifié au début du processus d’installation d’Azure AD Connect.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										 Vous avez tenté d’utiliser un utilisateur fédéré pour le compte d’administrateur général spécifié au début du processus d’installation d’Azure AD Connect.<br\><br\></li>
              </ul>
              <p>Pour corriger cette erreur, assurez-vous que vous n’utilisez pas un compte fédéré pour l’administrateur général que vous avez spécifié au début du processus d’installation d’Azure AD Connect et que le mot de passe spécifié est correct.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur de configuration de l’écriture différée lors de l’installation d’Azure AD Connect.</p>
            </td>
            <td>
              <p>Le journal des événements de l’ordinateur Azure AD Connect contient l’erreur&#160;32002 levée par PasswordResetService.</p>
              <p>
                
              </p>
              <p>L’erreur stipule&#160;: «&#160;Erreur lors de la connexion à ServiceBus. Le fournisseur de jetons était dans l’incapacité de fournir un jeton de sécurité...&#160;»</p>
              <p>
                
              </p>
            </td>
            <td>
              <p>La cause première de cette erreur est que le service de réinitialisation du mot de passe en cours d’exécution dans votre environnement local n’est pas en mesure de se connecter au point de terminaison Service Bus dans le cloud. Cette erreur est normalement due à une règle de pare-feu qui bloque une connexion sortante vers une adresse web ou un port spécifique.</p>
              <p>
                
              </p>
              <p>Assurez-vous que votre pare-feu autorise les connexions sortantes pour les éléments suivants&#160;:</p>
              <ul>
                <li class="unordered">
										Tout le trafic via TCP&#160;443 (HTTPS)<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Les connexions sortantes <br\><br\></li>
              </ul>
              <p>
                
              </p>
              <p>Une fois que vous avez mis à jour ces règles, redémarrez l’ordinateur Azure AD Connect. L’écriture différée des mots de passe doit fonctionner de nouveau.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Le point de terminaison local d’écriture différée des mots de passe n’est pas accessible.</p>
            </td>
            <td>
              <p>Après avoir pu réinitialiser leurs mots de passe pendant un certain temps, les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe ne parviennent plus à réinitialiser leurs mots de passe.</p>
            </td>
            <td>
              <p>Dans certains cas rares, le service d’écriture différée des mots de passe peut ne pas réussir à redémarrer quand Azure AD Connect a redémarré. Dans ce cas, commencez par vérifier si l’écriture différée des mots de passe est activée localement. Pour cela, utilisez l’Assistant Azure AD Connect ou PowerShell (voir les procédures ci-dessus). Si la fonctionnalité semble être activée, essayez de l’activer ou de la désactiver de nouveau par le biais de l’interface utilisateur ou de PowerShell. Consultez la rubrique «&#160;Étape&#160;2&#160;: activer l’écriture différée des mots de passe sur votre ordinateur de synchronisation d’annuaires &amp; configurer des règles de pare-feu &#160;» dans <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Comment activer/désactiver l’écriture différée des mots de passe</a> pour plus d’informations sur la procédure à suivre.</p>
              <p>
                
              </p>
              <p>Si le problème persiste, essayez de désinstaller et réinstaller Azure AD Connect complètement.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreurs d’autorisations</p>
            </td>
            <td>
              <p>Les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe qui tentent de réinitialiser leurs mots de passe voient une erreur après avoir soumis un mot de passe qui leur indique un problème du service.</p>
              <p>
                
              </p>
              <p>En outre, pendant les opérations de réinitialisation de mot de passe, une erreur peut figurer dans vos journaux des événements locaux, laquelle concerne l’agent de gestion auquel l’accès a été refusé.</p>
            </td>
            <td>
              <p>Si vous voyez ces erreurs dans votre journal des événements, vérifiez que le compte de l’agent de gestion AD (qui a été spécifié dans l’Assistant au moment de la configuration) dispose des autorisations nécessaires pour l’écriture différée des mots de passe.</p>
              <p>
                
              </p>
              <p>Notez qu’une fois que cette autorisation est accordée, cela peut prendre jusqu’à 1 heure pour que les autorisations arrivent via la tâche en arrière-plan sdprop sur le contrôleur de domaine.  </p>
              <p>Pour que la réinitialisation du mot de passe fonctionne, l’autorisation doit être marquée sur le descripteur de sécurité de l’objet utilisateur dont le mot de passe est réinitialisé. Tant que cette autorisation n’apparaît pas sur l’objet utilisateur, la réinitialisation du mot de passe continue d’échouer avec un accès refusé.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Erreur lors de la configuration de l’écriture différée des mots de passe depuis l’Assistant Configuration d’Azure AD Connect </p>
            </td>
            <td>
              <p>Erreur «&#160;Agent de gestion introuvable&#160;» dans l’Assistant lors de l’activation/la désactivation de l’écriture différée des mots de passe.</p>
            </td>
            <td>
              <p>Il existe un bogue connu dans la version finale d’Azure AD Connect qui se manifeste dans la situation suivante&#160;:</p>
              <ol class="ordered">
                <li>
										Vous configurez Azure AD Connect pour le client abc.com (domaine vérifié) à l’aide des informations d’identification. Un connecteur AAD portant le nom «&#160;abc.com&#160;- AAD&#160;» est alors créé.<br\><br\></li>
                <li>
										Vous modifiez ensuite les informations d’identification AAD du connecteur (à l’aide de l’ancienne interface utilisateur de synchronisation). (Notez qu’il s’agit du même client mais d’un nom de domaine différent). <br\><br\></li>
                <li>
										Vous essayez à présent d’activer/de désactiver l’écriture différée des mots de passe. L’Assistant crée le nom du connecteur en utilisant les informations d’identification, telles que «&#160;abc.onmicrosoft.com – AAD&#160;», puis le passe à l’applet de commande d’écriture différée des mots de passe. Cette opération échoue, car il n’existe aucun connecteur créé portant ce nom.<br\><br\></li>
              </ol>
              <p>Ce problème a été résolu dans nos versions les plus récentes. Si vous possédez une version plus ancienne, une solution de contournement consiste à utiliser l’applet de commande PowerShell pour activer ou désactiver la fonctionnalité. Consultez la rubrique «&#160;Étape&#160;2&#160;: activer l’écriture différée des mots de passe sur votre ordinateur de synchronisation d’annuaires &amp; configurer des règles de pare-feu &#160;» dans <a href="../active-directory-passwords-getting-started#enable-users-to-reset-or-change-their-ad-passwords">Comment activer/désactiver l’écriture différée des mots de passe</a> pour plus d’informations sur la procédure à suivre.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Impossible de réinitialiser le mot de passe des utilisateurs inclus dans des groupes spéciaux tels que les administrateurs de domaine/d’entreprise.</p>
            </td>
            <td>
              <p>Les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe qui font partie de groupes protégés et qui tentent de réinitialiser leurs mots de passe voient une erreur après avoir soumis un mot de passe qui leur indique un problème du service.</p>
            </td>
            <td>
              <p>Les utilisateurs privilégiés dans Active Directory sont protégés à l’aide d’AdminSDHolder. Consultez la page <a href="https://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx">http://technet.microsoft.com/magazine/2009.09.sdadminholder.aspx</a> pour plus de détails. </p>
              <p>
                
              </p>
              <p>Cela signifie que les descripteurs de sécurité sur ces objets sont régulièrement vérifiés pour correspondre à celui spécifié dans AdminSDHolder et qu’ils sont réinitialisés s’ils sont différents. Les autorisations supplémentaires nécessaires pour l’écriture différée des mots de passe n’arrivent donc pas à ces utilisateurs. Ainsi, l’écriture différée des mots de passe risque de ne pas fonctionner pour ces utilisateurs. Par conséquent, nous ne prenons pas en charge la gestion des mots de passe pour les utilisateurs au sein de ces groupes car elle rompt le modèle de sécurité AD.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échecs des opérations de réinitialisation avec objet introuvable</p>
            </td>
            <td>
              <p>Les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe qui tentent de réinitialiser leurs mots de passe voient une erreur après avoir soumis un mot de passe qui leur indique un problème du service.</p>
              <p>
                
              </p>
              <p>En outre, lors des opérations de réinitialisation de mot de passe, une erreur peut apparaître dans vos journaux des événements du service Azure AD Connect indiquant une erreur «&#160;Objet introuvable&#160;».</p>
            </td>
            <td>
              <p>Cette erreur indique généralement que le moteur de synchronisation est incapable de trouver l’objet utilisateur dans l’espace du connecteur AAD ou l’objet espace du connecteur MV ou AD lié. </p>
              <p>
                
              </p>
              <p>Pour résoudre ce problème, assurez-vous que l’utilisateur est véritablement synchronisé localement avec AAD via l’instance actuelle d’Azure AD Connect et examinez l’état des objets dans les espaces du connecteur et MV Vérifiez que l’objet AD CS est le connecteur à l’objet MV via la règle «&#160;Microsoft.InfromADUserAccountEnabled.xxx&#160;».</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Échecs des opérations de réinitialisation avec plusieurs correspondances trouvées générant une erreur</p>
            </td>
            <td>
              <p>Les utilisateurs fédérés ou qui ont recours à la synchronisation du hachage de mot de passe qui tentent de réinitialiser leurs mots de passe voient une erreur après avoir soumis un mot de passe qui leur indique un problème du service.</p>
              <p>
                
              </p>
              <p>En outre, lors des opérations de réinitialisation de mot de passe, une erreur peut apparaître dans vos journaux des événements du service Azure AD Connect indiquant une erreur «&#160;Plusieurs correspondances trouvées&#160;».</p>
            </td>
            <td>
              <p>Cette erreur indique que le moteur de synchronisation a détecté que l’objet MV est connecté à plusieurs objets AD CS via «&#160;Microsoft.InfromADUserAccountEnabled.xxx&#160;». Cela signifie que l’utilisateur dispose d’un compte activé dans plusieurs forêts. </p>
              <p>
                
              </p>
              <p>Ce scénario n’est actuellement pas pris en charge pour l’écriture différée de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Les opérations nécessitant un mot de passe échouent en renvoyant un message d’erreur de configuration.</p>
            </td>
            <td>
              <p>Les opérations nécessitant un mot de passe échouent en renvoyant un message d’erreur de configuration. Le journal des événements d’application contient le message d’erreur Azure AD Connect 6329 dont le texte est libellé comme suit&#160;: 0x8023061f (L’opération a échoué, car la synchronisation de mot de passe n’est pas activée pour cet agent de gestion).</p>
            </td>
            <td>
              <p>Cela se produit si la configuration d’Azure AD Connect est modifiée pour ajouter une nouvelle forêt AD (ou pour supprimer et rajouter une forêt existante) <strong>après</strong> l’activation de la fonctionnalité d’écriture différée du mot de passe. Les opérations nécessitant un mot de passe pour les utilisateurs dans de telles forêts nouvellement ajoutées échouent. Pour résoudre le problème, désactivez, puis réactivez la fonctionnalité d’écriture différée du mot de passe une fois les modifications de configuration de la forêt effectuées.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>L’écriture différé de mots de passe qui ont été réinitialisés par des utilisateurs fonctionne correctement, mais l’écriture différée de mots de passe modifiés par un utilisateur ou réinitialisés par un administrateur pour un utilisateur échoue.</p>
            </td>
            <td>
              <p>Lorsque vous essayez de réinitialiser un mot de passe pour le compte d’un utilisateur à partir du portail de gestion Azure, un message tel que celui ci-dessous s’affiche&#160;: «&#160;Le service de réinitialisation du mot de passe de votre environnement local ne prend pas en charge la réinitialisation des mots de passe utilisateur par les administrateurs. Pour résoudre ce problème, veuillez procéder à une mise à niveau vers la dernière version d’Azure AD Connect&#160;».</p>
            </td>
            <td>
              <p>Cela se produit lorsque la version du moteur de synchronisation ne prend pas en charge l’opération d’écriture différée de mot de passe qui a été utilisée. Les versions d’Azure AD Connect ultérieures à la version 1.0.0419.0911 prennent en charge toutes les opérations de gestion des mots de passe, dont l’écriture différée de réinitialisation du mot de passe, l’écriture différée de modification du mot de passe et l’écriture différée de réinitialisation du mot de passe initialisée par l’administrateur à partir du portail de gestion Azure.&#160; Les versions de DirSync ultérieures à la version&#160;1.0.6862 prennent en charge uniquement l’écriture différée de réinitialisation du mot de passe. Il est fortement recommandé d’installer la dernière version d’Azure AD Connect ou d’Azure Active Directory Connect (pour plus d’informations, consultez la rubrique <a href="../active-directory-aadconnect#download-azure-ad-connect">Outils d’intégration d’annuaires</a>) pour résoudre ce problème et tirer le meilleur parti de l’écriture différée de mot de passe dans votre organisation.</p>
            </td>
          </tr>
        </tbody></table>


## Codes d’erreur dans le journal des événements local de l’écriture différée des mots de passe
Pour résoudre les problèmes liés à l’écriture différée des mots de passe, nous vous recommandons d’examiner ce journal des événements de l’application sur votre ordinateur Azure AD Connect. Ce journal des événements contient des événements issus de deux sources d’intérêt pour l’écriture différée des mots de passe. La source PasswordResetService décrit les opérations et les problèmes liés au fonctionnement de l’écriture différée des mots de passe. La source ADSync décrit les opérations et les problèmes liés à la définition des mots de passe dans votre environnement AD.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Code</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Nom/Message</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Source</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Description</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>6329</p>
            </td>
            <td>
              <p>BAIL: MMS(4924) 0x80230619&#160;: «&#160;Une restriction empêche le mot de passe d’être remplacé par le mot de passe actuel spécifié.&#160;»</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit quand le service d’écriture différée des mots de passe tente de définir un mot de passe sur votre annuaire local qui ne respecte pas les critères d’ancienneté, d’historique, de complexité ou de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
										S’il existe une ancienneté minimale pour un mot de passe, alors que vous l’avez modifié avant le terme de cette ancienneté, vous ne pouvez pas le remodifier tant qu’il n’a pas atteint l’ancienneté spécifiée dans votre domaine. À des fins de test, l’ancienneté minimale doit être définie sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si des critères d’historique de mot de passe sont activés, vous devez sélectionner un mot de passe qui n’a pas été utilisé au cours des N dernières fois, où N est le paramètre d’historique du mot de passe. Si vous sélectionnez un mot de passe qui a été utilisé au cours des N dernières fois, un échec se produit. À des fins de test, l’historique doit être défini sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										S’il existe des critères de complexité de mot de passe, ils sont tous appliqués quand l’utilisateur tente de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si des filtres de mot de passe sont activés et qu’un utilisateur sélectionne un mot de passe qui ne répond pas aux critères de filtrage, alors l’opération de réinitialisation ou de modification échoue. <br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>HR 8023042</p>
            </td>
            <td>
              <p>Le moteur de synchronisation a renvoyé une erreur hr=80230402, message = Une tentative visant à obtenir un objet a échoué, car il existe des entrées en double avec le même point d’ancrage.</p>
            </td>
            <td>
              <p>ADSync</p>
            </td>
            <td>
              <p>Cet événement se produit lorsque le même identifiant utilisateur est activé dans plusieurs domaines. Par exemple, cette erreur peut se produire si vous synchronisez des forêts de comptes&#160;/ ressources et que le même identifiant utilisateur est présent et activé pour chacune d’elles.  </p>
              <p>Cette erreur peut également se produire si vous utilisez un attribut d'ancrage non unique (comme un alias ou un UPN) et que deux utilisateurs partagent ce même attribut d’ancrage.</p>
              <p>Pour résoudre ce problème, assurez-vous de ne pas avoir d’utilisateurs en double dans vos domaines et d’utiliser un attribut d’ancrage unique pour chaque utilisateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31001</p>
            </td>
            <td>
              <p>PasswordResetStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local a détecté une demande de réinitialisation de mot de passe pour un utilisateur fédéré ou qui a recours à la synchronisation du hachage de mot de passe provenant du cloud. Cet événement est le premier de chaque opération d’écriture différée de réinitialisation de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31002</p>
            </td>
            <td>
              <p>PasswordResetSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un mot de passe et que ce mot de passe est arrivé correctement dans l’environnement local, mais quand nous avons essayé de définir le mot de passe dans l’environnement AD local, une défaillance s’est produite.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31003</p>
            </td>
            <td>
              <p>PasswordResetFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un mot de passe et que ce mot de passe est arrivé correctement dans l’environnement local, mais quand nous avons essayé de définir le mot de passe dans l’environnement AD local, une défaillance s’est produite. Cela peut se produire pour plusieurs raisons&#160;:</p>
              <ul>
                <li class="unordered">
										Le mot de passe de l’utilisateur ne répond pas aux critères d’ancienneté, d’historique, de complexité ou de filtrage du domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte de service de l’agent de gestion n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte d’utilisateur est dans un groupe protégé, comme celui des administrateurs de domaine ou d’entreprise, ce qui n’autorise pas les opérations de définition de mots de passe.<br\><br\></li>
              </ul>
              <p>Consultez la rubrique <a href="#troubleshoot-password-writeback">Résoudre les problèmes liés à l’écriture différée des mots de passe</a> pour en savoir plus sur les autres situations pouvant provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31004</p>
            </td>
            <td>
              <p>OnboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous activez l’écriture différée des mots de passe avec Azure AD Connect et indique que nous avons commencé à intégrer votre organisation au service web d’écriture différée des mots de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31005</p>
            </td>
            <td>
              <p>OnboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le processus d’intégration a réussi et que la fonctionnalité d’écriture différée des mots de passe est prête à être utilisée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31006</p>
            </td>
            <td>
              <p>ChangePasswordStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local a détecté une demande de modification de mot de passe pour un utilisateur fédéré ou qui a recours à la synchronisation du hachage de mot de passe provenant du cloud. Cet événement est le premier de chaque opération d’écriture différée de modification de mot de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31007</p>
            </td>
            <td>
              <p>ChangePasswordSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un nouveau mot de passe pendant une opération de modification de mot de passe. Nous avons déterminé que ce mot de passe répond aux critères définis pour les mots de passe de l’entreprise et que ce mot de passe a été correctement écrit dans l’environnement AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31008</p>
            </td>
            <td>
              <p>ChangePasswordFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un utilisateur a sélectionné un mot de passe et que ce mot de passe est arrivé correctement dans l’environnement local, mais quand nous avons essayé de définir le mot de passe dans l’environnement AD local, une défaillance s’est produite. Cela peut se produire pour plusieurs raisons&#160;:</p>
              <ul>
                <li class="unordered">
										Le mot de passe de l’utilisateur ne répond pas aux critères d’ancienneté, d’historique, de complexité ou de filtrage du domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte de service de l’agent de gestion n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte d’utilisateur est dans un groupe protégé, comme celui des administrateurs de domaine ou d’entreprise, ce qui n’autorise pas les opérations de définition de mots de passe.<br\><br\></li>
              </ul>
              <p>Consultez la section <a href="#troubleshoot-password-writeback">Résoudre les problèmes liés à l’écriture différée des mots de passe</a> pour en savoir plus sur les autres situations pouvant provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31009</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Le service local a détecté une demande de réinitialisation de mot de passe pour un utilisateur fédéré ou qui a recours à la synchronisation du hachage de mot de passe provenant de l’administrateur au nom d’un utilisateur. Cet événement est le premier de chaque opération d’écriture différée de réinitialisation de mot de passe initialisée par l’administrateur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31010</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur a sélectionné un nouveau mot de passe pendant une opération de réinitialisation de mot de passe initialisée par l’administrateur. Nous avons déterminé que ce mot de passe répond aux critères définis pour les mots de passe de l’entreprise et que ce mot de passe a été correctement écrit dans l’environnement AD local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31011</p>
            </td>
            <td>
              <p>ResetUserPasswordByAdminFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>L’administrateur a sélectionné un mot de passe au nom d’un utilisateur et ce mot de passe est arrivé correctement dans l’environnement local, mais quand nous avons essayé de définir le mot de passe dans l’environnement AD local, une défaillance s’est produite. Cela peut se produire pour plusieurs raisons&#160;:</p>
              <ul>
                <li class="unordered">
										Le mot de passe de l’utilisateur ne répond pas aux critères d’ancienneté, d’historique, de complexité ou de filtrage du domaine. Essayez un tout nouveau mot de passe pour résoudre ce problème.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte de service de l’agent de gestion n’a pas les autorisations appropriées pour définir le nouveau mot de passe du compte d’utilisateur en question.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Le compte d’utilisateur est dans un groupe protégé, comme celui des administrateurs de domaine ou d’entreprise, ce qui n’autorise pas les opérations de définition de mots de passe.<br\><br\></li>
              </ul>
              <p>Consultez la rubrique <a href="#troubleshoot-password-writeback">Résoudre les problèmes liés à l’écriture différée des mots de passe</a> pour en savoir plus sur les autres situations pouvant provoquer cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31012</p>
            </td>
            <td>
              <p>OffboardingEventStart </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit si vous désactivez l’écriture différée des mots de passe avec Azure AD Connect et indique que nous avons commencé à désintégrer votre organisation du service web d’écriture différée des mots de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31013</p>
            </td>
            <td>
              <p>OffboardingEventSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le processus de désintégration a réussi et que la fonctionnalité d’écriture différée des mots de passe a correctement été désactivée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31014</p>
            </td>
            <td>
              <p>OffboardingEventFail </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le processus de désintégration n’a pas réussi. Cet échec peut être dû à une erreur des autorisations sur le compte d’administrateur local ou sur le cloud spécifié lors de la configuration. Il peut aussi être dû au fait que vous essayez d’utiliser un administrateur général fédéré sur le cloud pour désactiver l’écriture différée des mots de passe. Pour résoudre ce problème, vérifiez vos autorisations administratives et vérifiez que vous n’utilisez pas un compte fédéré pour configurer la fonctionnalité d’écriture différée des mots de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31015</p>
            </td>
            <td>
              <p>WriteBackServiceStarted </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service d’écriture différée des mots de passe a correctement démarré et qu’il est prêt à accepter des demandes de gestion de mot de passe provenant du cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31016</p>
            </td>
            <td>
              <p>WriteBackServiceStopped </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service d’écriture différée des mots de passe s’est arrêté et que toutes les demandes de gestion de mot de passe provenant du cloud vont échouer.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31017</p>
            </td>
            <td>
              <p>AuthTokenSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons correctement récupéré un jeton d’autorisation pour l’administrateur général spécifié pendant l’installation d’Azure AD Connect afin de démarrer le processus d’intégration ou de désintégration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>31018</p>
            </td>
            <td>
              <p>KeyPairCreationSuccess </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons correctement créé la clé de chiffrement de mot de passe qui sera utilisée pour chiffrer les mots de passe à partir du cloud et envoyée à votre environnement local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32000</p>
            </td>
            <td>
              <p>UnknownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur inconnue lors d’une opération de gestion de mot de passe. Examinez le texte de l’exception dans l’événement pour plus d’informations. Si vous rencontrez des problèmes, essayez de désactiver et de réactiver l’écriture différée des mots de passe. Si cela ne change rien, incluez une copie de votre journal des événements avec l’ID de suivi spécifié à l’ingénieur du support technique.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32001</p>
            </td>
            <td>
              <p>ServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’il s’est produit une erreur de connexion au service de réinitialisation du mot de passe cloud. Cette erreur se produit généralement quand le service local n’a pas pu se connecter au service web de réinitialisation de mot de passe. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32002</p>
            </td>
            <td>
              <p>ServiceBusError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique une erreur de connexion à une instance Service Bus de votre client. Cela peut se produire si vous bloquez les connexions sortantes de votre environnement local. Vérifiez que votre pare-feu autorise les connexions sur TCP&#160;443 et à <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a>, puis réessayez. Si vous rencontrez encore des problèmes, essayez de désactiver et de réactiver l’écriture différée des mots de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32003</p>
            </td>
            <td>
              <p>InPutValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’entrée passée à l’API du service web n’est pas valide. Recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32004</p>
            </td>
            <td>
              <p>DecryptionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’une erreur de déchiffrement du mot de passe arrivé du cloud s’est produite. Cette erreur peut provenir d’une incompatibilité de la clé de déchiffrement entre le service cloud et votre environnement local. Pour résoudre ce problème, désactivez et réactivez l’écriture différée des mots de passe dans votre environnement local.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32005</p>
            </td>
            <td>
              <p>ConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Au cours de l’intégration, nous enregistrons des informations propres au locataire dans un fichier de configuration dans votre environnement local. Cet événement indique qu’une erreur s’est produite lors de l’enregistrement de ce fichier ou, quand le service a démarré, lors de la lecture de ce fichier. Pour résoudre ce problème, essayez de désactiver et réactiver l’écriture différée des mots de passe pour forcer une réécriture de ce fichier de configuration. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32006</p>
            </td>
            <td>
              <p>EndPointConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ&#160;: Cet événement n’est pas présent dans Azure AD Connect, mais seulement dans les toutes premières versions de DirSync qui prenaient en charge l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32007</p>
            </td>
            <td>
              <p>OnBoardingConfigUpdateError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Pendant l’intégration, nous envoyons des données du cloud au service de réinitialisation du mot de passe local. Ces données sont ensuite écrites dans un fichier en mémoire avant d’être envoyées au service de synchronisation pour être stockées de manière sécurisée sur le disque. Cet événement indique un problème d’écriture ou de mise à jour de ces données en mémoire. Pour résoudre ce problème, essayez de désactiver et réactiver l’écriture différée des mots de passe pour forcer une réécriture de cette configuration.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32008</p>
            </td>
            <td>
              <p>ValidationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons reçu une réponse non valide du service web de réinitialisation du mot de passe. Pour résoudre ce problème, essayez de désactiver et réactiver l’écriture différée des mots de passe.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32009</p>
            </td>
            <td>
              <p>AuthTokenError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous n’avons pas réussi à obtenir un jeton d’autorisation pour le compte d’administrateur général spécifié pendant l’installation d’Azure AD Connect. Cette erreur peut être due à un mauvais nom d’utilisateur ou mot de passe spécifié pour le compte d’administrateur général ou au fait que le compte d’administrateur général spécifié est fédéré. Pour résoudre ce problème, réexécutez la configuration avec le nom d’utilisateur et le mot de passe corrects, puis assurez-vous que l’administrateur correspond à un compte géré (sur le cloud uniquement ou avec la synchronisation de mot de passe).</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32010</p>
            </td>
            <td>
              <p>CryptoError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’une erreur s’est produite lors de la génération de la clé de chiffrement du mot de passe ou lors du déchiffrement d’un mot de passe arrivé du service cloud. Cette erreur indique probablement un problème lié à votre environnement. Examinez les détails de votre journal des événements pour en savoir plus et résoudre ce problème. Vous pouvez également essayer de désactiver et réactiver le service d’écriture différée des mots de passe pour résoudre ce problème.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32011</p>
            </td>
            <td>
              <p>OnBoardingServiceError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local n’a pas pu communiquer correctement avec le service web de réinitialisation du mot de passe pour lancer le processus d’intégration. Cette erreur peut être due à une règle de pare-feu ou à un problème d’obtention d’un jeton d’autorisation pour votre client. Pour résoudre ce problème, assurez-vous de ne pas bloquer les connexions sortantes via TCP&#160;443 et TCP&#160;9350 à 9354 ou à <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> et que le compte d’administrateur AAD que vous utilisez pour l’intégration n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32012</p>
            </td>
            <td>
              <p>OnBoardingServiceDisableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ&#160;: Cet événement n’est pas présent dans Azure AD Connect, mais seulement dans les toutes premières versions de DirSync qui prenaient en charge l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32013</p>
            </td>
            <td>
              <p>OffBoardingError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le service local n’a pas pu communiquer correctement avec le service web de réinitialisation du mot de passe pour lancer le processus d’annulation de l’intégration. Cette erreur peut être due à une règle de pare-feu ou à un problème d’obtention d’un jeton d’autorisation pour votre client. Pour résoudre ce problème, assurez-vous de ne pas bloquer les connexions sortantes via TCP&#160;443 ou à <a href="https://ssprsbprodncu-sb.accesscontrol.windows.net/">https://ssprsbprodncu-sb.accesscontrol.windows.net/</a> et que le compte d’administrateur AAD que vous utilisez pour annuler l’intégration n’est pas fédéré. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32014</p>
            </td>
            <td>
              <p>ServiceBusWarning </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons dû réessayer de vous connecter à une instance Service Bus de votre client. Dans des conditions normales, cet événement n’est pas préoccupant, mais s’il se produit à de nombreuses reprises, envisagez de vérifier la connexion réseau au bus de service, surtout s’il s’agit d’une connexion à latence élevée ou à faible bande passante.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>32015</p>
            </td>
            <td>
              <p>ReportServiceHealthError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Pour analyser l’intégrité de votre service d’écriture différée des mots de passe, nous envoyons des données de pulsation à notre service web de réinitialisation du mot de passe toutes les 5 minutes. Cet événement indique qu’une erreur s’est produite lors du renvoi de ces informations d’intégrité au service web cloud. Ces informations d’intégrité n’incluent pas de données OII ou PII. Il s’agit purement de statistiques du service de pulsation et de base qui nous permettent de fournir des informations d’état du service dans le cloud.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33001</p>
            </td>
            <td>
              <p>ADUnKnownError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’une erreur inconnue a été renvoyée par AD. Consultez le journal des événements du serveur Azure AD Connect pour identifier les événements de la source ADSync pour plus d’informations sur cette erreur.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33002</p>
            </td>
            <td>
              <p>ADUserNotFoundError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur qui essaie de réinitialiser ou modifier un mot de passe est introuvable dans l’annuaire local. Cette erreur peut se produire quand l’utilisateur a été supprimé localement, mais pas sur le cloud, ou s’il existe un problème de synchronisation. Vérifiez vos journaux de synchronisation, ainsi que les détails de la dernière synchronisation pour plus d’informations.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33003</p>
            </td>
            <td>
              <p>ADMutliMatchError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Quand une demande de réinitialisation ou modification de mot de passe vient du cloud, nous utilisons l’ancrage cloud spécifiée pendant le processus d’installation d’Azure AD Connect pour déterminer comment lier cette demande à un utilisateur dans votre environnement local. Cet événement indique que nous avons trouvé deux utilisateurs dans votre annuaire local avec le même attribut d’ancrage cloud. Vérifiez vos journaux de synchronisation, ainsi que les détails de la dernière synchronisation pour plus d’informations.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33004</p>
            </td>
            <td>
              <p>ADPermissionsError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que le compte de service de l’agent de gestion n’a pas les autorisations appropriées sur le compte en question pour définir un nouveau mot de passe. Assurez-vous que le compte de l’agent de gestion dans la forêt de l’utilisateur possède des autorisations de réinitialisation et modification de mot de passe sur tous les objets de la forêt. Pour plus d’informations sur la façon de procéder, consultez la section <a href="../active-directory-passwords-getting-started#step-4-set-up-the-appropriate-active-directory-permissions">Étape&#160;4&#160;: définition des autorisations Active Directory appropriées</a>.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33005</p>
            </td>
            <td>
              <p>ADUserAccountDisabled </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons tenté de réinitialiser ou modifier un mot de passe pour un compte qui a été désactivé localement. Activez le compte et recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33006</p>
            </td>
            <td>
              <p>ADUserAccountLockedOut </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que nous avons tenté de réinitialiser ou modifier un mot de passe pour un compte qui a été verrouillé localement. Des verrouillages peuvent se produire quand un utilisateur a tenté une opération de modification ou réinitialisation de mot de passe un nombre de fois trop élevé sur un court laps de temps. Déverrouillez le compte et recommencez l’opération.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33007</p>
            </td>
            <td>
              <p>ADUserIncorrectPassword </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique que l’utilisateur a spécifié un mot de passe actuel incorrect lors de l’opération de modification du mot de passe. Spécifiez le mot de passe correct actuel et réessayez.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>33008</p>
            </td>
            <td>
              <p>ADPasswordPolicyError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement se produit quand le service d’écriture différée des mots de passe tente de définir un mot de passe sur votre annuaire local qui ne respecte pas les critères d’ancienneté, d’historique, de complexité ou de filtrage du domaine.</p>
              <ul>
                <li class="unordered">
										S’il existe une ancienneté minimale pour un mot de passe, alors que vous l’avez modifié avant le terme de cette ancienneté, vous ne pouvez pas le remodifier tant qu’il n’a pas atteint l’ancienneté spécifiée dans votre domaine. À des fins de test, l’ancienneté minimale doit être définie sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si des critères d’historique de mot de passe sont activés, vous devez sélectionner un mot de passe qui n’a pas été utilisé au cours des N dernières fois, où N est le paramètre d’historique du mot de passe. Si vous sélectionnez un mot de passe qui a été utilisé au cours des N dernières fois, un échec se produit. À des fins de test, l’historique doit être défini sur 0.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										S’il existe des critères de complexité de mot de passe, ils sont tous appliqués quand l’utilisateur tente de modifier ou réinitialiser un mot de passe.<br\><br\></li>
              </ul>
              <ul>
                <li class="unordered">
										Si des filtres de mot de passe sont activés et qu’un utilisateur sélectionne un mot de passe qui ne répond pas aux critères de filtrage, alors l’opération de réinitialisation ou de modification échoue. <br\><br\></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>33009</p>
            </td>
            <td>
              <p>ADConfigurationError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>Cet événement indique qu’un problème s’est produit lors de l’écriture d’un mot de passe dans votre annuaire local en raison d’un problème de configuration avec Active Directory. Consultez le journal des événements de l’application de l’ordinateur Azure AD Connect pour identifier les messages provenant du service ADSync et obtenir plus d’informations sur l’erreur qui s’est produite. </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34001</p>
            </td>
            <td>
              <p>ADPasswordPolicyOrPermissionError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ&#160;: Cet événement n’est pas présent dans Azure AD Connect, mais seulement dans les toutes premières versions de DirSync qui prenaient en charge l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34002</p>
            </td>
            <td>
              <p>ADNotReachableError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ&#160;: Cet événement n’est pas présent dans Azure AD Connect, mais seulement dans les toutes premières versions de DirSync qui prenaient en charge l’écriture différée.</p>
            </td>
          </tr>
          <tr>
            <td>
              <p>34003</p>
            </td>
            <td>
              <p>ADInvalidAnchorError </p>
            </td>
            <td>
              <p>PasswordResetService</p>
            </td>
            <td>
              <p>DÉCONSEILLÉ&#160;: Cet événement n’est pas présent dans Azure AD Connect, mais seulement dans les toutes premières versions de DirSync qui prenaient en charge l’écriture différée.</p>
            </td>
          </tr>
        </tbody></table>
## Résolution des problèmes de connectivité de l’écriture différée de mot de passe

Si vous rencontrez des interruptions de service avec le composant d’écriture différée de mot de passe d’Azure AD Connect, voici quelques mesures rapides à prendre pour résoudre le problème :

 - [Redémarrer le service de synchronisation Azure AD Connect](#restart-the-azure-AD-sync-service)
 - [Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe](#disable-and-re-enable-the-password-writeback-feature)
 - [Installer la dernière version d’Azure AD Connect](#install-the-latest-azure-ad-connect-release)
 - [Résoudre les problèmes d’écriture différée de mot de passe](#troubleshoot-password-writeback)

En général, nous vous recommandons d’exécuter ces étapes dans l’ordre ci-dessus afin de récupérer votre service de la manière la plus rapide.

### Redémarrer le service de synchronisation d’Azure AD Connect
Le redémarrage du service de synchronisation d’Azure AD Connect peut aider à résoudre les problèmes de connectivité ou d’autres problèmes temporaires rencontrés avec le service.

 1.	En tant qu’administrateur, cliquez sur **Démarrer** sur le serveur exécutant **Azure AD Connect**.
 2.	Tapez **« services.msc »** dans la zone de recherche et appuyez sur **Entrée**.
 3.	Recherchez l’entrée **Microsoft Azure AD Connect**.
 4.	Cliquez avec le bouton droit sur l’entrée du service, cliquez sur **Redémarrer** et attendez que l’opération soit terminée.

    ![][002]

Ces étapes vont rétablir votre connexion au service cloud et résoudre toute interruption à laquelle vous pouvez être confronté. Si le redémarrage du service de synchronisation ne résout pas votre problème, nous vous recommandons d’essayer de le désactiver et de réactiver la fonctionnalité d’écriture différée de mot de passe comme prochaine étape.

### Désactiver et réactiver la fonctionnalité d’écriture différée de mot de passe
La désactivation et la réactivation de la fonctionnalité d’écriture différée de mot de passe peuvent aider à résoudre les problèmes de connectivité.

 1.	En tant qu’administrateur, ouvrez l’**Assistant Configuration d’Azure AD Connect**.
 2.	Dans la boîte de dialogue **Connexion à Azure AD**, entrez vos **informations d’identification d’administrateur général d’Azure AD**
 3.	Dans la boîte de dialogue **Connexion à AD DS**, entrez vos **informations d’identification d’administrateur des services de domaine AD**
 4.	Dans la boîte de dialogue **Identification de vos utilisateurs uniquement**, cliquez sur le bouton **Suivant**.
 5.	Dans la boîte de dialogue **Fonctionnalités facultatives**, désactivez la case à cocher **Écriture différée de mot de passe**.

    ![][003]

 6.	Cliquez sur **Suivant** dans les pages restantes de la boîte de dialogue sans apporter de modification jusqu’à ce que vous atteigniez la page **Prêt pour la configuration**.
 7.	Vérifiez que la page de configuration indique l’**option d’écriture différée de mot de passe comme désactivée**, puis cliquez sur le bouton vert **Configurer** pour valider vos modifications.
 8.	Dans la boîte de dialogue **Terminé**, désactivez l’option **Synchroniser maintenant**, puis cliquez sur **Terminer** pour fermer l’Assistant.
 9.	Ouvrez à nouveau l’**Assistant Configuration d’Azure AD Connect**.
 10.	**Répétez les étapes 2 à 8**, en vous assurant cette fois que vous **activez l’option d’écriture différée de mot de passe** dans l’écran **Fonctionnalités facultatives** pour réactiver le service.

    ![][004]

Ces étapes vont rétablir votre connexion au service cloud et résoudre toute interruption à laquelle vous pouvez être confronté.

Si la désactivation et la réactivation de la fonctionnalité d’écriture différée de mot de passe ne résout pas votre problème, nous vous recommandons d’essayer de réinstaller Azure AD Connect.

### Installer la dernière version d’Azure AD Connect
La réinstallation du package Azure AD Connect peut résoudre les problèmes de configuration susceptibles d’affecter votre capacité à vous connecter à nos services cloud ou à gérer les mots de passe de votre environnement Active Directory local. Il est préférable d’effectuer cette étape uniquement après avoir tenté les deux premières étapes décrites ci-dessus.

 1.	Téléchargez la dernière version d’Azure AD Connect [ici](active-directory-aadconnect.md#download-azure-ad-connect).
 2.	Puisque vous avez déjà installé Azure AD Connect, il vous suffit d’effectuer une mise à niveau sur place pour mettre à jour votre installation d’Azure AD Connect vers la dernière version.
 3.	Exécutez le package téléchargé et suivez les instructions à l’écran pour mettre à jour votre ordinateur Azure AD Connect. Aucune opération manuelle supplémentaire n’est requise, sauf si vous avez personnalisé les règles de synchronisation par défaut, auquel cas vous devez **les sauvegarder avant de procéder à la mise à niveau et les déployer à nouveau manuellement une fois que vous avez terminé**.

Ces étapes vont rétablir votre connexion au service cloud et résoudre toute interruption à laquelle vous pouvez être confronté.

Si l’installation de la dernière version du serveur Azure AD Connect ne résout pas votre problème, la dernière étape recommandée consiste à essayer la désactivation et la réactivation de l’écriture différée de mot de passe après l’installation du dernier correctif QFE de synchronisation.

Si cela ne résout pas votre problème, nous vous recommandons de consulter la rubrique [Résoudre les problèmes liés à l’écriture différée des mots de passe](#troubleshoot-password-writeback) et [FAQ sur la gestion des mots de passe Azure AD](active-directory-passwords-faq.md) pour voir s’il n’y est pas déjà exposé.


<br/> <br/> <br/>

## Liens vers la documentation de réinitialisation du mot de passe
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* [**Réinitialiser votre mot de passe**](active-directory-passwords-update-your-own-password.md) : découvrez la procédure de réinitialisation ou de modification de votre mot de passe en tant qu'utilisateur du système.
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux.
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Meilleures pratiques**](active-directory-passwords-best-practices.md) : découvrez comment déployer et gérer rapidement et efficacement les mots de passe de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**FAQ**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.



[001]: ./media/active-directory-passwords-troubleshoot/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-troubleshoot/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-troubleshoot/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-troubleshoot/004.jpg "Image_004.jpg"

<!---HONumber=AcomDC_1125_2015-->