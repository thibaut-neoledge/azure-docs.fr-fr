<properties
	pageTitle="Flux Azure Active Directory Identity Protection | Microsoft Azure"
	description="Fournit une vue d’ensemble de l’expérience utilisateur lorsque Identity Protection a atténué ou corrigé la connexion d’un utilisateur ou lorsque l’authentification multifacteur est requise par une stratégie."
	services="active-directory"
	keywords="azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="markvi"/>

#Flux Azure Active Directory Identity Protection

Avec Azure Active Directory Identity Protection, vous pouvez :

- exiger que les utilisateurs s’inscrivent à l’authentification multifacteur

- gérer les connexions risquées et les utilisateurs compromis

La réponse du système à ces problèmes a un impact sur l’expérience de connexion d’un utilisateur, car la connexion à l’aide d’un nom d’utilisateur et d’un mot de passe seulement ne sera plus possible. Des étapes supplémentaires sont nécessaires pour rétablir l’accès d’un utilisateur en toute sécurité.

Cette rubrique donne une vue d’ensemble de l’expérience de connexion d’un utilisateur pour tous les cas qui peuvent se produire.

**Authentification multifacteur**

- Inscription à l’authentification multifacteur



**Connexion risquée**

- Récupération de connexion à risque

- Connexion à risque bloquée

- Inscription à l’authentification multifacteur au cours d’une connexion à risque
 

**Utilisateur à risque**

- Récupération de compte compromis

- Compte compromis bloqué




## Inscription à l’authentification multifacteur

L’utilisateur bénéficie d’une expérience optimale pour le flux de récupération de compte compromis et de connexion à risque lorsqu’il peut effectuer lui-même l’opération de récupération. Si des utilisateurs sont inscrits à l’authentification multifacteur, ils ont déjà un numéro de téléphone associé à leur compte qu’ils peuvent utiliser pour répondre aux questions de sécurité. La récupération d’un compte suite à sa compromission ne nécessite pas l’intervention du support technique ou d’un administrateur. Par conséquent, nous vous recommandons vivement de demander à vos utilisateurs de s’inscrire à l’authentification multifacteur.

Les administrateurs peuvent :

- définir une stratégie qui impose aux utilisateurs d’ajouter une vérification de sécurité supplémentaire à leur compte ; 
- autoriser les utilisateurs à ignorer l’inscription à l’authentification multifacteur pendant 30 jours maximum, s’ils souhaitent leur accorder un délai de grâce avant l’inscription.

**L’inscription à l’authentification multifacteur comporte trois étapes :**

1. Dans la première étape, l’utilisateur reçoit une notification concernant la nécessité d’inscrire le compte à l’authentification multifacteur. <br><br> ![Correction](./media/active-directory-identityprotection-flows/140.png "Correction") <br>


2. Pour configurer l’authentification multifacteur, vous devez indiquer au système comment vous souhaitez être contacté. <br><br> ![Correction](./media/active-directory-identityprotection-flows/141.png "Correction") <br>
 
3. Le système vous envoie un défi et vous devez y répondre. <br><br> ![Correction](./media/active-directory-identityprotection-flows/142.png "Correction") <br>

 



## Récupération de connexion à risque

Lorsqu’un administrateur a configuré une stratégie pour les risques à la connexion, les utilisateurs affectés sont avertis quand ils tentent de se connecter.

**Le flux de connexion à risque comporte deux étapes :**

1. L’utilisateur est informé que quelque chose d’inhabituel a été détecté concernant sa connexion, par exemple en cas de connexion depuis un nouvel emplacement, un nouvel appareil ou une nouvelle application. <br> <br> ![Correction](./media/active-directory-identityprotection-flows/120.png "Correction") <br>

2. L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il doit saisir un code de sécurité envoyé sur son téléphone. Comme il s’agit simplement d’une connexion à risque et non pas d’un compte compromis, l’utilisateur ne doit pas changer le mot de passe dans ce flux. <br> <br> ![Correction](./media/active-directory-identityprotection-flows/121.png "Correction") <br>



 
## Connexion à risque bloquée
Les administrateurs peuvent également choisir de définir une stratégie en matière de risque à la connexion pour bloquer les utilisateurs lors de la connexion selon le niveau de risque. Pour débloquer leur accès, les utilisateurs finaux doivent contacter un administrateur ou leur support technique, ou ils peuvent essayer de se connecter depuis un emplacement ou un appareil connu. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis. <br><br> ![Correction](./media/active-directory-identityprotection-flows/130.png "Correction") <br>



## Inscription à l’authentification multifacteur au cours d’une connexion à risque

Il est important que les utilisateurs s’inscrivent à l’authentification multifacteur afin qu’ils soient prêts à répondre aux demandes de sécurité et en mesure de le faire. Si un utilisateur n’est pas inscrit à l’authentification multifacteur, mais que la stratégie exige qu’il le soit, il se peut qu’il soit invité à s’inscrire pendant une connexion à risque. Cela signifie qu’un cybercriminel peut se retrouver à devoir ajouter un numéro de téléphone à la place de l’utilisateur correct.

Pour éviter cette situation, exigez des utilisateurs qu’ils s’inscrivent à l’authentification multifacteur dès que possible, afin qu’un numéro de téléphone soit déjà associé à leur compte en cas de compromission de ce dernier. Les administrateurs peuvent également bloquer complètement les utilisateurs compromis qui ne sont pas inscrits à l’authentification multifacteur.

**L’inscription à l’authentification multifacteur au cours d’une connexion à risque comporte deux étapes :**

1. L’utilisateur est averti que le compte est à risque. <br><br> ![Correction](./media/active-directory-identityprotection-flows/150.png "Correction") <br>

2. Le processus d’inscription à l’authentification multifacteur est initié. <br><br> ![Correction](./media/active-directory-identityprotection-flows/151.png "Correction") <br>

Pour les étapes suivantes, consultez [Inscription à l’authentification multifacteur](#multi-factor-authentication-registration)




## Récupération de compte compromis

Lorsqu’une stratégie de sécurité en matière de risque des utilisateurs a été configurée, les utilisateurs dont le niveau de risque correspond à celui spécifié dans la stratégie (et qui sont donc considérés comme compromis) doivent passer par le flux de récupération de compte compromis avant de pouvoir se connecter.

**Le flux de récupération de compte compromis comporte trois étapes :**

1. L’utilisateur est informé que la sécurité de son compte est menacée en raison d’activités suspectes ou de la divulgation de ses informations d’identification.

<br> ![Correction](./media/active-directory-identityprotection-flows/101.png "Correction") <br>

2.	L’utilisateur doit prouver son identité en répondant à une question de sécurité. Si l’utilisateur est inscrit à l’authentification multifacteur, il peut récupérer lui-même son compte compromis. Il devra saisir un code de sécurité envoyé sur son téléphone. 

<br> ![Correction](./media/active-directory-identityprotection-flows/110.png "Correction") <br>


3.	Enfin, l’utilisateur est obligé de changer son mot de passe, car il se peut que quelqu’un d’autre ait eu accès à son compte. Vous trouverez ci-dessous des captures d’écran de cette expérience.
 
<br> ![Correction](./media/active-directory-identityprotection-flows/111.png "Correction") <br>



## Compte compromis bloqué 

Pour débloquer un compte bloqué par une stratégie de sécurité en matière de risque des utilisateurs, l’utilisateur doit contacter un administrateur ou son support technique. Il n’a pas la possibilité de récupérer lui-même son compte en résolvant l’authentification multifacteur dans ce cas précis.

<br> ![Correction](./media/active-directory-identityprotection-flows/104.png "Correction") <br>



 
## Réinitialiser le mot de passe

Si des utilisateurs compromis voient leur connexion bloquée, un administrateur peut générer un mot de passe temporaire pour eux. Les utilisateurs devront changer leur mot de passe la prochaine fois qu’ils se connecteront.

<br> ![Correction](./media/active-directory-identityprotection-flows/160.png "Correction") <br>


 




 

## Voir aussi

- [Azure Active Directory Identity Protection](active-directory-identityprotection.md) 

<!---------HONumber=AcomDC_0309_2016-->