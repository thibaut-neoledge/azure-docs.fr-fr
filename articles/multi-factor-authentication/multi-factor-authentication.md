<properties 
	pageTitle="Azure Multi-Factor Authentication - De quoi s'agit-il ?" 
	description="Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l'utilisation d'autres méthodes que le nom d'utilisateur et le mot de passe. Ce service fournit une couche de sécurité supplémentaire pour les connexions et les transactions de l'utilisateur." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadwha" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="hero-article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>

# Présentation d'Azure Multi-Factor Authentication
L'authentification multi-facteur est une méthode d'authentification qui nécessite l'utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. Cette authentification fonctionne en nécessitant au minimum deux des méthodes de vérification suivantes :

- Un élément que vous connaissez (généralement un mot de passe)
- Un élément que vous possédez (un appareil de confiance qui n'est pas facilement dupliqué, comme un téléphone)
- Un élément vous concernant particulièrement (biométrie)

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>



Azure Multi-Factor Authentication est une méthode permettant de vérifier votre identité qui requiert l'utilisation d'autres méthodes que le nom d'utilisateur et le mot de passe. Ce service fournit une deuxième couche de sécurité pour les connexions et les transactions de l'utilisateur.

Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il fournit une authentification forte via diverses options de vérification simples : appel téléphonique, message texte, notification par application mobile ou code de vérification et jetons OATH tiers.

Pour une vue d'ensemble du fonctionnement d'Azure Multi-Factor Authentication, regardez la vidéo suivante.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##Pourquoi utiliser Azure Multi-Factor Authentication ?

Aujourd'hui, plus que jamais, les personnes sont de plus en plus connectées. Avec les smartphones, les tablettes, les PC portables et les PC de bureau, les utilisateurs ont plusieurs options différentes pour se connecter et rester connecté à tout moment. Ils peuvent accéder à leurs comptes et leurs applications où qu'ils soient, et cela signifie qu'ils peuvent être plus productifs et mieux servir leurs clients.

Azure Multi-Factor Authentication est une solution facile à utiliser, évolutive et fiable qui fournit une deuxième méthode d'authentification pour que vos utilisateurs soient toujours protégés.

![Facile à utiliser](./media/multi-factor-authentication/simple.png)| ![Extensibilité](./media/multi-factor-authentication/scalable.png)| ![Toujours protégé](./media/multi-factor-authentication/protected.png)|![Fiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Facile à utiliser**|**Évolutif**|**Toujours protégé**|**Fiable**

- **Facile à utiliser** : Azure Multi-Factor Authentication est simple à configurer et utiliser. La protection supplémentaire offerte par Azure Multi-Factor Authentication permet aux utilisateurs d'utiliser et de gérer leurs propres appareils et, dans de nombreux cas, elle peut être configurée en quelques clics simples.
- **Évolutif** : Azure Multi-Factor Authentication utilise la puissance du cloud et s'intègre à votre site Active Directory local et vos applications personnalisées. Cette protection est même étendue à vos scénarios stratégiques volumineux.
- **Toujours protégé** : Azure Multi-Factor Authentication fournit une authentification forte qui utilise les normes les plus strictes du secteur.
- **Fiable** : nous garantissons une disponibilité à 99,9 % d'Azure Multi-Factor Authentication. Le service est considéré comme non disponible quand il ne parvient pas à recevoir ou traiter des demandes d'authentification pour l'authentification multifacteur.  

Pour plus d'informations sur la raison pour laquelle vous devez utiliser Azure Multi-Factor Authentication, regardez la vidéo suivante.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**Ressources supplémentaires**

* [Configurer l’authentification multifacteur pour Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)
* [Quelles sont les implications de l’application Multi-Factor Authentication pour moi ?](multi-factor-authentication-end-user.md)

<!---HONumber=August15_HO7-->