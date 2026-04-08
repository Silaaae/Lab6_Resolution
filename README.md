# Lab6_Resolution


Lab 6 — Mobile Security Framework (MobSF)

1. Informations générales
ChampValeurAPK analyséUnCrackable-Level2.apkTaille880 KBDate d'analyse2026-04-07Analyste[Ton nom]Outil utiliséMobSF via DockerVMMobexlerScore de sécurité36/100

2. Résumé exécutif
L'analyse statique de l'application UnCrackable-Level2 révèle un niveau de risque élevé (score 36/100). Les principales vulnérabilités concernent le mode debug activé, la détection de root, et des secrets potentiellement codés en dur dans le code natif.

3. Vulnérabilités identifiées
🔴 Vulnérabilité 1 — Application en mode Debug

Sévérité : Critique
Référence MASVS : MASVS-RESILIENCE-2
Description : L'attribut android:debuggable="true" est activé dans le manifeste. En production, cela permet à un attaquant de s'attacher à l'application avec un débogueur.
Localisation : AndroidManifest.xml — élément <application>
Impact : Un attaquant peut inspecter la mémoire, modifier le comportement de l'application à l'exécution, et extraire des données sensibles.
Recommandation : Désactiver android:debuggable en production. Gradle le fait automatiquement en mode release.

<img width="957" height="928" alt="image" src="https://github.com/user-attachments/assets/4151d00d-f171-4924-a5df-4a3ec343f029" />
<img width="1918" height="985" alt="image" src="https://github.com/user-attachments/assets/ffb894a8-14cf-460f-b35e-6e13261c84c1" />

🔴 Vulnérabilité 2 — Secrets codés en dur (code natif)

Sévérité : Critique
Référence MASVS : MASVS-STORAGE-2
Description : Des chaînes de caractères sensibles (clés, secrets) sont stockées en clair dans la bibliothèque native .so.
Localisation : Bibliothèque native lib/ — fichier .so
Impact : Un attaquant peut extraire ces secrets par rétro-ingénierie de la bibliothèque native avec des outils comme strings ou radare2.
Recommandation : Ne jamais stocker de secrets en dur dans le code. Utiliser un serveur sécurisé ou Android Keystore.

<img width="1440" height="857" alt="image" src="https://github.com/user-attachments/assets/10c53da5-8203-4203-81e7-1e6890718dd6" />
<img width="954" height="1143" alt="image" src="https://github.com/user-attachments/assets/e2401356-f032-4629-843f-63ea49119204" />


🟠 Vulnérabilité 3 — Détection de root insuffisante

Sévérité : Élevée
Référence MASVS : MASVS-RESILIENCE-1
Description : L'application tente de détecter si le device est rooté, mais ces mécanismes peuvent être contournés facilement avec des outils comme Frida ou Magisk Hide.
Localisation : MainActivity.java
Impact : Un attaquant sur un device rooté peut contourner les protections et accéder aux données internes.
Recommandation : Implémenter plusieurs mécanismes de détection combinés et utiliser des solutions de vérification d'intégrité (ex: Play Integrity API).


🟠 Vulnérabilité 4 — Absence de configuration de sécurité réseau

Sévérité : Moyenne
Référence MASVS : MASVS-NETWORK-1
Description : Aucun fichier network_security_config.xml n'est présent. L'application utilise la configuration réseau par défaut d'Android.
Localisation : res/xml/ — fichier absent
Impact : Sans configuration explicite, l'application peut être vulnérable aux attaques de type Man-in-the-Middle sur des versions Android anciennes.
Recommandation : Ajouter un fichier network_security_config.xml qui force le trafic HTTPS et désactive le trafic HTTP en clair.


🟡 Vulnérabilité 5 — Permissions potentiellement excessives

Sévérité : Faible
Référence MASVS : MASVS-PLATFORM-1
Description : L'application déclare des permissions dont la nécessité n'est pas justifiée par ses fonctionnalités apparentes.
Localisation : AndroidManifest.xml
Impact : Surface d'attaque élargie inutilement.
Recommandation : Appliquer le principe du moindre privilège — ne demander que les permissions strictement nécessaires.


4. Recommandations prioritaires

Désactiver le mode debug — Retirer android:debuggable="true" du manifeste ou configurer Gradle pour le désactiver automatiquement en mode release.
Supprimer les secrets du code natif — Déplacer toute clé ou secret vers un serveur sécurisé ou Android Keystore.
Ajouter une configuration réseau — Créer network_security_config.xml pour bloquer le trafic HTTP non chiffré.
Renforcer la détection de root — Combiner plusieurs méthodes et utiliser la Play Integrity API.
Revoir les permissions — Supprimer toute permission non justifiée par les fonctionnalités de l'application.

<img width="922" height="1020" alt="image" src="https://github.com/user-attachments/assets/539e7534-1ba8-4244-9bc9-00894076923d" />

Rapport d'audit de sécurité — Analyse statique APK
5. Corrélation OWASP MASVS
VulnérabilitéRéférence MASVSCatégorieMode debug activéMASVS-RESILIENCE-2ResilienceSecrets codés en durMASVS-STORAGE-2StorageDétection de root insuffisanteMASVS-RESILIENCE-1ResilienceAbsence config réseauMASVS-NETWORK-1NetworkPermissions excessivesMASVS-PLATFORM-1Platform

6. Annexes
Permissions déclarées

Permissions normales : INTERNET, ACCESS_NETWORK_STATE
Permissions dangereuses : à compléter selon rapport MobSF
<img width="957" height="1147" alt="image" src="https://github.com/user-attachments/assets/2cc122b3-1f31-4b05-b77c-13189e7b3565" />

Composants exportés

2/17 Exported Activities détectées
0/0 Exported Services
0/0 Exported Receivers
1/1 Exported Providers (⚠️ à vérifier)
<img width="1114" height="974" alt="image" src="https://github.com/user-attachments/assets/0e255da6-81f6-4b11-bb10-7349fee0968a" />
Outils utilisés

MobSF (Mobile Security Framework) via Docker
VM Mobexler
Firefox pour accès interface web MobSF
