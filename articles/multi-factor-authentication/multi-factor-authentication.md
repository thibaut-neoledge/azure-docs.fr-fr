<properties
	pageTitle="Azure Multi-Factor Authentication - De quoi s'agit-il ? | Microsoft Azure"
	description="Cette rubrique explique en quoi consiste l’authentification multifacteur (mfa), pourquoi utiliser l’authentification multifacteur, fournit plus d’informations sur le client d’authentification multifacteur, ainsi que les différentes méthodes et versions disponibles. Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l'utilisation d'autres méthodes que le nom d'utilisateur et le mot de passe. Ce service fournit une couche de sécurité supplémentaire pour les connexions et les transactions de l’utilisateur."
	keywords="introduction à l'authentification multifacteur, vue d'ensemble de l'authentification multifacteur, définition de l'authentification multifacteur"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="kgremban"/>

# Présentation d'Azure Multi-Factor Authentication
L’authentification multi-facteur est une méthode d’authentification qui nécessite plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Cette authentification fonctionne en nécessitant au minimum deux des méthodes de vérification suivantes :

- Un élément que vous connaissez (généralement un mot de passe)
- Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
- Un élément vous concernant particulièrement (biométrie)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>


Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une authentification forte via diverses options de vérification simples : appel téléphonique, SMS, vérification sur l’application mobile.

>[AZURE.VIDEO multi-factor-authentication-overview]

## Pourquoi utiliser Azure Multi-Factor Authentication ?

Aujourd'hui, plus que jamais, les personnes sont de plus en plus connectées. Avec les smartphones, les tablettes, les PC portables et les PC de bureau, les utilisateurs ont plusieurs options différentes pour se connecter et rester connecté à tout moment. Ils peuvent accéder à leurs comptes et leurs applications où qu’ils soient, cela signifie qu’ils peuvent être plus productifs et mieux servir leurs clients.

Azure Multi-Factor Authentication est une solution facile à utiliser, évolutive et fiable qui fournit une deuxième méthode d'authentification pour que vos utilisateurs soient toujours protégés.

![Facile à utiliser](./media/multi-factor-authentication/simple.png)| ![Extensibilité](./media/multi-factor-authentication/scalable.png)| ![Toujours protégé](./media/multi-factor-authentication/protected.png)|![Fiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Facile à utiliser**|**Évolutif**|**Toujours protégé**|**Fiable**

- **Facile à utiliser** : Azure Multi-Factor Authentication est simple à configurer et utiliser. La protection supplémentaire qui est fournie avec l’authentification multifacteur Azure permet aux utilisateurs de gérer leurs propres appareils. De plus, dans de nombreux cas elle peut être configurée de manière très simple.
- **Évolutif** : Azure Multi-Factor Authentication utilise la puissance du cloud et s’intègre à votre site Active Directory local et à vos applications personnalisées. Cette protection est même étendue à vos scénarios stratégiques volumineux.
- **Toujours protégé** : Azure Multi-Factor Authentication fournit une authentification forte qui utilise les normes les plus strictes du secteur.
- **Fiable** : nous garantissons une disponibilité à 99,9 % d'Azure Multi-Factor Authentication. Le service est considéré comme non disponible quand il ne parvient pas à recevoir ou traiter des demandes d'authentification pour l'authentification multifacteur.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## Azure Multi-Factor Authentication : fonctionnement

La sécurité de l'authentification multi-facteur repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître votre mot de passe, ce dernier est inutile sans posséder l’appareil de confiance. Si l’utilisateur perd l’appareil, la personne qui l’a trouvé ne pourra pas l’utiliser sans connaître votre mot de passe.

![Vérification](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples :

- appel téléphonique
- SMS
- notification sur l’application mobile, ce qui permet aux utilisateurs de choisir leur méthode préférée
- code de vérification sur l’application mobile
- jetons OATH tiers

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Méthodes disponibles pour l'authentification multifacteur
Lorsqu'un utilisateur se connecte, une vérification supplémentaire lui est envoyée. Voici la liste des méthodes qui peuvent être utilisées pour cette seconde vérification.

Méthode de vérification | Description
------------- | ------------- |
Appel téléphonique | Un appel est passé sur le téléphone de l’utilisateur demandant de vérifier qu’il se connecte. Appuyez sur la touche # pour finaliser le processus de vérification. Cette option est configurable et peut être remplacée par un code que vous spécifiez.
SMS | Un SMS sera envoyé sur le smartphone de l’utilisateur avec un code à 6 chiffres. Entrez ce code pour finaliser le processus de vérification.
Notification sur l’application mobile | Une demande de vérification sera envoyée sur le smartphone de l’utilisateur lui indiquant de terminer la vérification en sélectionnant **Vérifier** depuis l’application mobile. Cela se produit si une notification de l’application est la méthode de vérification principale. Si l’utilisateur reçoit cette notification lorsqu’il ne se connecte pas, il peut la signaler comme fraude.</li><br><p> L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).
Code de vérification avec l’application mobile | Un code de vérification est envoyé sur l’application mobile qui s’exécute sur le smartphone de l’utilisateur. Cela se produit si vous avez sélectionné un code de vérification comme méthode de vérification principale.</li><br><p> L'application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).


## Versions disponibles d’Azure Multi-Factor Authentication
Azure Multi-Factor Authentication est disponible en trois versions. Le tableau ci-dessous décrit chacune de ces versions en détail.

Version | Description
------------- | ------------- |
Authentification multi-facteur pour Office 365 | Cette version fonctionne exclusivement avec les applications Office 365 et est gérée à partir du portail Office 365. De ce fait, les administrateurs peuvent désormais sécuriser leurs ressources Office 365 avec une authentification multifacteur. Cette version est fournie avec un abonnement à Office 365.
Authentification multi-facteur pour administrateurs Azure | Le même sous-ensemble de fonctionnalités d’authentification multi-facteur pour Office 365 est disponible sans frais à tous les administrateurs Azure. Tout compte administratif possédant un abonnement Azure peut désormais bénéficier d’une protection supplémentaire en activant cette fonctionnalité principale d’authentification multi-facteur. Un administrateur qui souhaite accéder au portail Azure afin de créer une machine virtuelle, un site web, gérer le stockage, ou utiliser tout autre service Azure peut ajouter une authentification multi-facteur à son compte d’administrateur.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication offre un riche éventail de fonctionnalités. Il fournit des options de configuration supplémentaires via le [Portail Azure Classic](http://manage.windowsazure.com), des fonctions de rapports avancées et la prise en charge d’une sélection d’applications locales et dans le cloud. Azure Multi-Factor Authentication est fourni avec Azure Active Directory Premium et Enterprise Mobility Suite.

## Comparaison des fonctionnalités suivant les versions
Le tableau suivant fournit la liste des fonctionnalités qui sont disponibles dans les différentes versions d’Azure Multi-Factor Authentication.


Fonctionnalité | Multi-Factor Authentication pour Office 365 (fourni dans les SKU Office 365)|Multi-Factor Authentication pour les administrateurs Azure (fourni avec un abonnement Azure) | Azure Multi-Factor Authentication (fourni dans Azure AD Premium et Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Les administrateurs peuvent protéger les comptes avec MFA| ● | ● (Disponible uniquement pour les comptes d’administrateur Azure)|●
Application mobile comme second facteur|● | ● | ●
Appel téléphonique comme second facteur|● | ● | ●
SMS comme second facteur|● | ● | ●
Mots de passe d'application pour les clients qui ne prennent pas en charge MFA|● | ● | ●
Contrôle d'administration sur les méthodes d'authentification| ● | ● | ●
Mode du code PIN| | | ●
Alerte de fraude| | | ●
Rapports MFA| | | ●
Contournement à usage unique| | | ●
Messages de bienvenue personnalisés pour les appels téléphoniques| | | ●
Personnalisation de l'ID d'appelant pour les appels téléphoniques| | | ●
Confirmation d’événement| | | ●
Adresses IP approuvées| | | ●
Mémoriser MFA pour les appareils fiables |● | ● | ●
SDK MFA | | | ● requiert un fournisseur Multi-Factor Auth et un abonnement Azure
MFA pour des applications locales utilisant le serveur MFA| | | ●

## Comment obtenir Azure Multi-Factor Authentication ?

Si vous souhaitez tirer parti de l’ensemble des fonctionnalités offertes par Azure Multi-Factor Authentication, en plus de celles fournies aux utilisateurs d’Office 365 et administrateurs Azure, plusieurs options s’offrent à vous :

1.	Achetez des licences Azure Multi-Factor Authentication et attribuez-les à vos utilisateurs.
2.	Achetez des licences regroupant Azure Multi-Factor Authentication (comme Azure Active Directory Premium, Enterprise Mobility Suite ou Enterprise Cloud Suite) et attribuez-les à vos utilisateurs.
3.	Créez un fournisseur Azure Multi-Factor Authentication dans un abonnement Azure. Si vous ne disposez pas d’un abonnement Azure, vous pouvez vous inscrire pour souscrire à un abonnement Azure d’évaluation. Les abonnements d’évaluation doivent être convertis en abonnements standard avant leur date d’expiration.

Quand vous utilisez un fournisseur Azure Multi-Factor Authentication, vous avez le choix entre deux modèles d’utilisation qui sont facturés dans le cadre de votre abonnement Azure :


- **Par utilisateur**. Pour les entreprises qui veulent activer l'authentification multifacteur pour un nombre fixe d'employés qui s'authentifient régulièrement.
- **Par authentification**. Pour les entreprises qui veulent activer l'authentification multifacteur pour un nombre important d’utilisateurs externes qui s'authentifient ponctuellement.

Azure Multi-Factor Authentication fournit des méthodes de vérification sélectionnables pour cloud et pour serveur. Cela signifie que vous pouvez choisir les méthodes disponibles que vos utilisateurs peuvent utiliser avec l’authentification multifacteur. Cette fonctionnalité est actuellement en version préliminaire publique pour la version cloud de l’authentification multifacteur. Pour plus d’informations, consultez [Méthodes de vérification sélectionnables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Pour plus d’informations sur la tarification, consultez la [tarification d’Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Choisissez le modèle qui convient le mieux à votre organisation : celui basé sur le nombre d’utilisateurs ou celui basé sur la consommation. Puis, pour la prise en main, reportez-vous à [Mise en route](multi-factor-authentication-get-started.md).

## Étapes suivantes

Pour commencer avec l’authentification multifacteur Azure, votre première étape consiste à [choisir entre l’authentification multifacteur dans le cloud ou en local](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0928_2016-->