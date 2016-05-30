<properties
   pageTitle="Directives de personnalisation des applications | Microsoft Azure"
   description="Guide complet sur les ressources destinées aux développeurs pour Azure Active Directory"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/16/2016"
   ms.author="mbaldwin"/>


# Directives de personnalisation des applications


Cette rubrique décrit les directives de personnalisation que vous devez suivre lors du développement d’applications avec Azure Active Directory. Ces directives vous permettent de diriger vos clients quand ils souhaitent utiliser leur compte professionnel ou scolaire géré dans Azure AD pour s’inscrire et se connecter à votre application.

## Comptes personnels et comptes professionnels/scolaires de Microsoft

Microsoft gère deux types de compte d’utilisateur :

- **Comptes personnels** (anciennement Windows Live ID). Ces comptes représentent la relation entre des utilisateurs *individuels* et Microsoft et sont utilisés pour accéder aux services et appareils de Microsoft destinés au grand public. Ces comptes sont prévus pour un usage personnel.

- **Comptes professionnels ou scolaires.** Ces comptes sont gérés par Microsoft pour le compte d’organisations qui utilisent Azure Active Directory. Ces comptes sont utilisés pour se connecter à Office 365 et d’autres services métier de Microsoft.

Les comptes professionnels ou scolaires Microsoft sont généralement affectés à des utilisateurs finaux (employés, étudiants, fonctionnaires) par leurs organisations (entreprise, école, administration). Ces comptes sont régis directement dans le cloud, dans la plateforme Azure AD, ou synchronisés avec Azure AD à partir d’un annuaire local tel que Windows Server Active Directory. Microsoft est le *responsable* des comptes professionnels ou scolaires, mais ces comptes sont détenus et contrôlés par l’organisation.

## Faire référence aux comptes Azure AD dans votre application

Microsoft n’expose pas les utilisateurs finaux aux noms de marque Azure ou Active Directory et il doit en être de même pour vous.

- Une fois que les utilisateurs sont connectés, vous devez utiliser le nom et le logo de l’organisation autant que possible. Cela est préférable à l’utilisation de termes génériques tels que « votre organisation ».

- Quand les utilisateurs ne sont pas connectés, vous devez faire référence à leurs comptes en parlant de « comptes professionnels ou scolaires » et utiliser le logo Microsoft pour indiquer que ces comptes sont gérés par Microsoft. N’utilisez pas de termes tels que « compte d’entreprise » et « compte commercial », car ils embrouillent les utilisateurs.

## Pictogramme de compte d’utilisateur
Dans une version antérieure de ces directives, nous recommandions d’utiliser un pictogramme de « badge bleu ». Suite aux commentaires des utilisateurs et des développeurs, nous recommandons désormais d’utiliser le logo Microsoft. Cela permet aux utilisateurs de comprendre qu’ils peuvent réutiliser le compte dont ils se servent avec Office 365 ou d’autres services métier Microsoft pour se connecter à votre application.

## Inscription et connexion avec Azure AD

Votre application peut présenter des chemins distincts pour l’inscription et la connexion, et les sections suivantes fournissent des conseils visuels pour les deux scénarios.

**Si votre application prend en charge l’inscription des utilisateurs finaux (par exemple, pour les modèles d’évaluation gratuits ou Premium)** : vous pouvez afficher un bouton de **connexion** qui permet aux utilisateurs d’accéder à votre application avec leur compte professionnel ou scolaire de Microsoft. Azure AD affiche une invite de consentement la première fois que les utilisateurs accèdent à votre application.

**Si votre application nécessite des autorisations auxquelles seuls les administrateurs peuvent consentir ou si votre application requiert une licence d’entreprise** : vous devez séparer l’acquisition administrateur de la connexion utilisateur. Le **bouton « obtenir cette application »** redirige les administrateurs vers la connexion, après quoi ils sont invités à donner leur consentement pour le compte des utilisateurs de leur organisation. Cela présente l’avantage supplémentaire de supprimer de votre application les invites de consentement pour les utilisateurs finaux.

## Aide visuelle pour l’acquisition de l’application

Le lien « obtenir l’application » doit rediriger l’utilisateur vers la page d’octroi d’accès d’Azure AD, afin de permettre à l’administrateur d’une organisation d’autoriser votre application à accéder aux données de son organisation hébergées par Microsoft. Pour plus d'informations sur la demande d'accès, consultez l'article [Intégration d'applications dans Azure Active Directory](active-directory-integrating-applications.md).

Une fois que les administrateurs ont donné leur consentement à votre application, ils peuvent l’ajouter au lanceur d’applications Office 365 de leurs utilisateurs (accessible à partir de l’icône correspondante dans Office 365 et de [https://portal.office.com/myapps](https://portal.office.com/myapps)). Pour faire la promotion de cette fonctionnalité, vous pouvez utiliser des phrases telles que « Ajouter cette application à votre organisation » et afficher un bouton tel que celui-ci :

![Types d’application et scénarios](./media/active-directory-branding-guidelines/add-to-my-org.png)

Toutefois, nous vous recommandons de fournir un texte explicatif plutôt que de vous contenter des boutons. Par exemple :
> *Si vous utilisez déjà Office 365 ou un autre service métier de Microsoft, vous pouvez tout simplement octroyer <your_app_name> l’accès aux données de votre organisation. Cela permet à vos utilisateurs d’y accéder à <your_app_name> l’aide de leur compte professionnel existant.*


## Aide visuelle pour la connexion
Votre application doit afficher un bouton de connexion qui redirige les utilisateurs vers le point de terminaison de connexion correspondant au protocole que vous utilisez pour l’intégration à Azure AD. La section suivante fournit des détails sur l’aspect recommandé pour ce bouton.

### Pictogramme et « compte professionnel ou scolaire »
C’est l’association du logo Microsoft et du terme générique « professionnel ou scolaire » qui permet de distinguer Azure AD des autres fournisseurs d’identité que votre application peut prendre en charge. Si vous n’avez pas suffisamment d’espace pour insérer « compte professionnel ou scolaire », vous pouvez abréger et utiliser « compte professionnel ».

![Types d’application et scénarios](./media/active-directory-branding-guidelines/work-or-school-account.png)

![Types d’application et scénarios](./media/active-directory-branding-guidelines/work-account.png)

Vous pouvez également fournir une explication supplémentaire pour aider les utilisateurs finaux à déterminer s’ils peuvent utiliser ce bouton :

![Types d’application et scénarios](./media/active-directory-branding-guidelines/work-account-with-explaination.png)

## Choses à faire et à éviter en matière de personnalisation
**UTILISEZ** l’expression « compte professionnel ou scolaire » avec le logo Microsoft pour représenter les connexions avec Azure AD. Si l’espace est limité, vous pouvez dire « compte professionnel », n’utilisez **PAS** d’autres termes tels que « compte d’entreprise » ou « compte commercial ».

N’utilisez **PAS** « ID Office 365 » ni « ID Azure ». Office 365 est également le nom d’une offre grand public de Microsoft qui n’utilise pas Azure AD pour l’authentification.

Ne modifiez **PAS** le logo Microsoft.

N’exposez **PAS** les utilisateurs finaux à la marque Azure ou Active Directory. Il est toutefois tout à fait concevable d’utiliser ces termes avec les développeurs, les professionnels de l’informatique et les administrateurs.

## Choses à faire et à éviter pour la navigation

**FOURNISSEZ** aux utilisateurs un moyen de se déconnecter et de passer à un autre compte d’utilisateur. Bien que la plupart des gens aient un seul compte personnel Microsoft/Facebook/Google/Twitter, ils sont souvent associés à plus d’une organisation. La prise en charge de plusieurs utilisateurs connectés sera bientôt offerte.

## Prise en charge d’Azure AD et des comptes Microsoft dans votre application

Si votre application prend en charge Azure AD et les comptes Microsoft, vous devez y inclure deux boutons de connexion distincts. Nous travaillons actuellement sur une mise à jour qui vous permettra d’effectuer une seule intégration et de prendre en charge à la fois les comptes personnels et professionnels de Microsoft. Une fois que cette fonctionnalité sera disponible, vous serez en mesure d’afficher un seul bouton « connexion avec Microsoft » dans votre application.

<!---HONumber=AcomDC_0518_2016-->