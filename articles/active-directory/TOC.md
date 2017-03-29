# Vue d'ensemble
## [Qu’est-ce qu’Azure Active Directory ?](active-directory-whatis.md)
## [Choisir l’édition](active-directory-editions.md)
## [À propos de la gestion des identités Azure](fundamentals-identity.md)
## [Afficher un aperçu de l’expérience du portail Azure AD](active-directory-preview-explainer.md)


# Prise en main
## [Obtention d’un locataire Azure AD](active-directory-howto-tenant.md)
## [S’inscrire à Azure AD Premium](active-directory-get-started-premium.md)
## [Associer les abonnements Azure](active-directory-how-subscriptions-associated-directory.md)
## Gérer les licences Azure AD
### [Portail Azure](active-directory-licensing-get-started-azure-portal.md)
### [Portail classique](active-directory-licensing-what-is.md)
## [Obtenir Azure pour votre organisation](sign-up-organization.md)
## [FAQ](active-directory-faq.md)
## [Didacticiels sur les applications SaaS](active-directory-saas-tutorial-list.md)

# Procédures
## Planifier et concevoir
### [Déployer une solution d’identité hybride](active-directory-hybrid-identity-design-considerations-overview.md)
#### Déterminer la configuration requise
##### [Identité](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Synchronisation d’annuaires](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Serveurs MFA](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Stratégie votre cycle de vie des identités](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planifier la sécurité des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Protection des données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planifier votre cycle de vie des identités
##### [Tâches :](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Stratégie d’adoption](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Étapes suivantes](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparaison d’outils](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gestion des utilisateurs
### Ajouter des utilisateurs
#### [Portail Azure](active-directory-users-create-azure-portal.md)
#### [Portail classique](active-directory-create-users.md)

### [Ajouter des utilisateurs à partir d’autres répertoires (portail classique)](active-directory-create-users-external.md)
### [Suppression d’utilisateurs](active-directory-users-delete-user-azure-portal.md)
### [Gérer les profils utilisateur](active-directory-users-profile-azure-portal.md)
### [Réinitialiser un mot de passe](active-directory-users-reset-password-azure-portal.md)
### [Gérer les informations de travail utilisateur](active-directory-users-work-info-azure-portal.md)
### [Partager des comptes](active-directory-sharing-accounts.md)

## [Gérer les groupes et les membres](active-directory-manage-groups.md)
### Gérer les groupes
#### [portail Azure](active-directory-groups-create-azure-portal.md)
#### [Portail classique](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Sélectionner les membres du groupe](active-directory-groups-members-azure-portal.md)
### [Gérer les propriétaires de groupe](active-directory-accessmanagement-managing-group-owners.md)
### [Ajouter une appartenance à un groupe](active-directory-groups-membership-azure-portal.md)
### [Afficher tous les groupes](active-directory-groups-view-azure-portal.md)
### [Activer les groupes dédiés](active-directory-accessmanagement-dedicated-groups.md)
### [Ajouter l’accès de groupe aux applications SaaS](active-directory-accessmanagement-group-saasapps.md)
### Gérer les paramètres de groupe
#### [Portail Azure](active-directory-groups-settings-azure-portal.md)
#### [Applets de commande](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Créer des règles avancées
#### [Portail Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portail classique](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Gestion des licences par groupe](active-directory-licensing-whatis-azure-portal.md)
#### [Attribuer des licences à un groupe](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identifier et résoudre les problèmes de licence d’un groupe](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrer des utilisateurs individuels titulaires d’une licence aux licences par groupe](active-directory-licensing-group-migration-azure-portal.md)
#### [Scénarios supplémentaires pour la gestion des licences par groupe](active-directory-licensing-group-advanced.md)
### [Configurer des groupes libre-service](active-directory-accessmanagement-self-service-group-management.md)
### [Résolution des problèmes](active-directory-accessmanagement-troubleshooting.md)

## [Gérer les rapports](active-directory-reporting-azure-portal.md)
### [Activité des connexions](active-directory-reporting-activity-sign-ins.md)
### [Activité des audits](active-directory-reporting-activity-audit-logs.md)
### [Utilisateurs à risque](active-directory-reporting-security-user-at-risk.md)
### [Connexions risquées](active-directory-reporting-security-risky-sign-ins.md)
### [Événements à risque](active-directory-reporting-risk-events.md)
### [Réseaux nommés](active-directory-known-networks-azure-portal.md)
### [Migration des rapports](active-directory-reporting-migration.md)
### [Rétention](active-directory-reporting-retention.md)
### [FAQ](active-directory-reporting-faq.md)
### Résolution des problèmes
#### [Données d’audit manquantes](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Données manquantes dans les téléchargements](active-directory-reporting-troubleshoot-missing-data-download.md)
###    Accès par programme
#### [Référence d’audit](active-directory-reporting-api-audit-reference.md)
#### [Exemples d’audit](active-directory-reporting-api-audit-samples.md)
#### [Configuration requise](active-directory-reporting-api-prerequisites.md)
#### [Référence de connexion](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Exemples de connexion](active-directory-reporting-api-sign-in-activity-samples.md)
### [Portail classique](active-directory-view-access-usage-reports.md)
#### [Génération de rapports Azure AD](active-directory-reporting-getting-started.md)
#### [Guide sur la génération de rapports](active-directory-reporting-guide.md)
#### [Réseaux connus](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [Événements d'audit](active-directory-reporting-audit-events.md)
#### [Latences](active-directory-reporting-latencies.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### Comprendre les rapports
##### [Connexion anormale](active-directory-reporting-irregular-sign-in-activity.md)
##### [Plusieurs échecs ](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [Adresses IP suspectes](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [Plusieurs zones géographiques](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [Appareils potentiellement infectés](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [Sources inconnues](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [Connexions anormales](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [Gestion des mots de passe](active-directory-manage-passwords.md)
### [Mettre à jour votre mot de passe](active-directory-passwords-update-your-own-password.md)
### [Configurer la réinitialisation du mot de passe libre-service](active-directory-passwords.md)
### [Comprendre la gestion des mots de passe](active-directory-passwords-how-it-works.md)
### [Comprendre les restrictions et stratégies](active-directory-passwords-policy.md)
### Réinitialiser les mots de passe
#### [Portail Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portail classique](active-directory-create-users-reset-password.md)
### [Définir des stratégies d’expiration](active-directory-passwords-set-expiration-policy.md)
### Activer la gestion des mots de passe
#### [Prise en main](active-directory-passwords-getting-started.md)
#### [Déployer](active-directory-passwords-best-practices.md)
#### [Personnaliser](active-directory-passwords-customize.md)
#### [Afficher des rapports](active-directory-passwords-get-insights.md)
#### [En savoir plus](active-directory-passwords-learn-more.md)
#### [FAQ](active-directory-passwords-faq.md)
#### [Résolution des problèmes](active-directory-passwords-troubleshoot.md)

## Gérer des appareils
### [Inscrire des appareils](active-directory-device-registration-overview.md)
#### [Gérer l’inscription automatique](active-directory-device-registration.md)
#### [Paramétrage](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Déployer en local](active-directory-device-registration-on-premises-setup.md)
#### [FAQ](active-directory-device-registration-faq.md)
#### Résolution des problèmes
##### [Résolution des problèmes pour Windows 10 et Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
##### [Résolution des problèmes pour les clients de bas niveau Windows](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Azure AD Join](active-directory-azureadjoin-overview.md)
#### [Planification](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Configuration de l’inscription des appareils](active-directory-azureadjoin-setup.md)
#### [Inscrire de nouveaux appareils](active-directory-azureadjoin-user-frx.md)
#### [Déployer](active-directory-azureadjoin-devices-group-policy.md)
#### [Comprendre l’intégration de Windows 10](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Utiliser des appareils Windows 10](active-directory-azureadjoin-windows10-devices.md)
#### [Joindre votre appareil](active-directory-azureadjoin-personal-device.md)
#### [Joindre un appareil Windows 10](active-directory-azureadjoin-user-upgrade.md)

## Gérer des applications
### [Vue d’ensemble](active-directory-enable-sso-scenario.md)
### [Prise en main](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [Mettre à jour les paramètres du Registre](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [Comprendre la sécurité et la confidentialité](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Octroyer l’accès à distance aux services](active-directory-application-proxy-get-started.md)
#### [Activer le Proxy d’application](active-directory-application-proxy-enable.md)
#### [Comprendre les connecteurs](application-proxy-understand-connectors.md)

#### Publier des applications
##### [Portail Azure](application-proxy-publish-azure-portal.md)
##### [Portail classique](active-directory-application-proxy-publish.md)

#### [PingAccess pour Azure AD](application-proxy-ping-access.md)
#### [Sécurité](application-proxy-security-considerations.md)
#### [Réseaux](application-proxy-network-topology-considerations.md)
#### [Bureau à distance](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)

#### Publier sur des réseaux distincts
##### [Portail Azure](active-directory-application-proxy-connectors-azure-portal.md)
##### [Portail classique](active-directory-application-proxy-connectors.md)
#### [Serveurs proxy](application-proxy-working-with-proxy-servers.md)
#### [Domaines personnalisés](active-directory-application-proxy-custom-domains.md)
#### [Accéder aux applications](active-directory-appssoaccess-whatis.md)
##### [portail Azure](application-proxy-sso-azure-portal.md)
#### [Authentification unique](active-directory-application-proxy-sso-using-kcd.md)
#### [Applications prenant en charge les revendications](active-directory-application-proxy-claims-aware-apps.md)
#### [Applications de client natif](active-directory-application-proxy-native-client.md)
#### [Page d’accueil personnalisée](application-proxy-office365-app-launcher.md)
#### [Accès conditionnel](active-directory-application-proxy-conditional-access.md)
#### [Installation sans assistance](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [Résolution des problèmes](active-directory-application-proxy-troubleshoot.md)

### Gérer les applications d’entreprise
#### [Affecter des utilisateurs](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personnaliser la marque](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Comment désactiver les connexions des utilisateurs ?](active-directory-coreapps-disable-app-azure-portal.md)
#### [Supprimer des utilisateurs](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Afficher toutes mes applications](active-directory-coreapps-view-azure-portal.md)
#### [Gérer l’approvisionnement de comptes d’utilisateurs](active-directory-enterprise-apps-manage-provisioning.md)

### Développement
#### [Affecter des utilisateurs](active-directory-applications-guiding-developers-assigning-users.md)
#### [Affecter des groupes](active-directory-applications-guiding-developers-assigning-groups.md)
#### [Affectation requise](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [Développer des applications métier](active-directory-applications-guiding-developers-for-lob-applications.md)

### [Gérer l’accès aux applications](active-directory-managing-access-to-apps.md)
#### [Accès libre-service](active-directory-self-service-application-access.md)
#### [Certificats pour authentification unique](active-directory-sso-certs.md)
#### [Restrictions du client](active-directory-tenant-restrictions.md)

### [Utilisez SCIM approvisionner les utilisateurs](active-directory-scim-provisioning.md)
### [Bibliothèque de documents ](active-directory-apps-index.md)

## Gérer votre répertoire
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Noms de domaine personnalisés
#### [Vue d'ensemble](active-directory-add-domain-concepts.md)
#### Obtenir votre nom de domaine
##### [Portail Azure](active-directory-domains-add-azure-portal.md)
##### [Portail classique](active-directory-add-domain.md)
##### [Avec AD FS](active-directory-add-domain-federated.md)
#### [Affecter des utilisateurs](active-directory-add-domain-add-users.md)
#### Gérer les noms de domaine
##### [portail Azure](active-directory-domains-manage-azure-portal.md)
##### [Portail classique](active-directory-add-manage-domain-names.md)
### Personnaliser la page de connexion
#### [portail Azure](active-directory-branding-custom-signon-azure-portal.md)
#### [Spécifiques au langage](active-directory-branding-localize-azure-portal.md)
#### [Portail classique](active-directory-add-company-branding.md)
### [Administration de votre annuaire](active-directory-administer.md)
### [Plusieurs répertoires](active-directory-licensing-directory-independence.md)
### [Répertoires O365](active-directory-manage-o365-subscription.md)
### [Inscription libre-service](active-directory-self-service-signup.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Activer](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Paramètres de stratégie de groupe](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Paramètres Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Résolution des problèmes](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Intégrer des partenaires avec Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Ajout d’utilisateurs B2B par des administrateurs](active-directory-b2b-admin-add-users.md)
#### [Ajout d’utilisateurs B2B par des membres de l’équipe informatique](active-directory-b2b-iw-add-users.md)
#### [E-mail d’invitation](active-directory-b2b-invitation-email.md)
#### [Échange d’invitation](active-directory-b2b-redemption-experience.md)
#### [Propriétés de l’utilisateur B2B](active-directory-b2b-user-properties.md)
#### [Ajouter un utilisateur invité à un rôle](active-directory-b2b-add-guest-to-role.md)
#### [Audit et création de rapports](active-directory-b2b-auditing-and-reporting.md)
#### [API et personnalisation](active-directory-b2b-api.md)
#### [Déléguer des invitations](active-directory-b2b-delegate-invitations.md)
#### [Groupes dynamiques et B2B](active-directory-b2b-dynamic-groups.md)
#### [Authentification multifacteur pour B2B](active-directory-b2b-mfa-instructions.md)
#### [Jetons utilisateur B2B](active-directory-b2b-user-token.md)
#### [Mappage des revendications d’utilisateur B2B](active-directory-b2b-claims-mapping.md)
#### [Partage externe d’Office 365](active-directory-b2b-o365-external-user.md)
#### [Configuration des applications SaaS pour B2B](active-directory-b2b-configure-saas-apps.md)
#### [Code et exemples PowerShell](active-directory-b2b-code-samples.md)
#### [Limitations actuelles](active-directory-b2b-current-limitations.md)
#### [Licences](active-directory-b2b-licensing.md)
#### [Résolution de problèmes B2B](active-directory-b2b-troubleshooting.md)
#### [Comparer la collaboration B2B et B2C](active-directory-b2b-compare-b2c.md)
#### [Obtenir une assistance pour B2B](active-directory-b2b-support.md)
#### [FORUM AUX QUESTIONS](active-directory-b2b-faq.md)
### [Intégrer des identités locales à l’aide d’Azure AD Connect](./connect/active-directory-aadconnect.md)


## Déléguer l’accès aux ressources
### [Rôles d’administrateur](active-directory-assign-admin-roles.md)
#### [Affecter des rôles d’administrateur](active-directory-users-assign-role-azure-portal.md)
### [Unités administratives](active-directory-administrative-units-management.md)
### [Accès aux ressources dans Azure](active-directory-understanding-resource-access.md)
### [Contrôle d’accès en fonction du rôle](role-based-access-control-what-is.md)
#### Gérer les affectations d’accès
##### [Par utilisateur](role-based-access-control-manage-assignments.md)
##### [Par ressource](role-based-access-control-configure.md)
#### [Rôles intégrés](role-based-access-built-in-roles.md)
#### [Rôles personnalisés](role-based-access-control-custom-roles.md)
#### [Reporting](role-based-access-control-access-change-history-report.md)
#### Plus de méthodes pour gérer les rôles
##### [Interface de ligne de commande Azure](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Résolution des problèmes](role-based-access-control-troubleshooting.md)
### [Durées de vie des jetons](active-directory-configurable-token-lifetimes.md)

## Sécuriser vos identités
### [Accès conditionnel](active-directory-conditional-access.md)
#### [Prise en main](active-directory-conditional-access-azuread-connected-apps.md)
#### [Applications prises en charge](active-directory-conditional-access-supported-apps.md)
#### [Comprendre les stratégies d’appareils](active-directory-conditional-access-device-policies.md)
#### [Configurer l’accès aux applications connectées](active-directory-conditional-access-policy-connected-applications.md)
#### [FAQ](active-directory-conditional-faqs.md)
#### [Résolution des problèmes](active-directory-conditional-access-device-remediation.md)
#### [Référence](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [S’authentifier sans mots de passe](active-directory-azureadjoin-passport.md)
#### [Activer Windows Hello Entreprise](active-directory-azureadjoin-passport-deployment.md)
### Authentification par certificat
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Activer](active-directory-identityprotection-enable.md)
#### [Détecter les vulnérabilités](active-directory-identityprotection-vulnerabilities.md)
#### [Événements à risque](active-directory-identity-protection-risk-events.md)
#### [Notifications](active-directory-identityprotection-notifications.md)
#### [Expérience de connexion](active-directory-identityprotection-flows.md)
#### [Simuler des événements à risque](active-directory-identityprotection-playbook.md)
#### [Débloquer des utilisateurs](active-directory-identityprotection-unblock-howto.md)
#### [Glossaire](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Déployer AD DS sur des machines virtuelles Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory sur les machines virtuelles Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Contrôleur de domaine de réplication dans un réseau virtuel Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Nouvelle forêt sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)



## [Déployer AD FS dans Azure](active-directory-aadconnect-azure-adfs.md)
### [Haute disponibilité](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Algorithme de hachage de signature de modification](active-directory-federation-sha256-guidance.md)

## [Résolution des problèmes](active-directory-troubleshooting.md)


# Référence
## [Applets de commande PowerShell](/powershell/ )
## [Informations de référence sur l’API Java](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restrictions et limites du service](active-directory-service-limits-restrictions.md)

# Rubriques connexes
## [Azure Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure pour développeurs](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# les ressources
## [Tarification](https://azure.microsoft.com/pricing/details/active-directory/)
## [Forum MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vidéos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Mises à jour de service](https://azure.microsoft.com/updates/?product=active-directory)
## [Forum de commentaires](https://feedback.azure.com/forums/169401-azure-active-directory)
