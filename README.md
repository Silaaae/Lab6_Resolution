

# Rapport d'audit de sécurité — Analyse statique APK
## Lab 6 — Mobile Security Framework (MobSF)

---

## 1. Informations générales

| Champ | Valeur |
|---|---|
| **APK analysé** | UnCrackable-Level2.apk |
| **Taille** | 0.86 MB |
| **Package** | owasp.mstg.uncrackable2 |
| **SHA256** | 4c7980ef1f6cc29508f38417c2a5b9b7721eeb4a3d7f00a0c1a08f5192fa6ddd |
| **Date d'analyse** | 2026-04-08 |
| **Analyste** | [Ton nom] |
| **Outil utilisé** | MobSF v4.0.6 via Docker |
| **VM** | Mobexler |
| **Score de sécurité** | 65/100 |

<img width="957" height="928" alt="image" src="https://github.com/user-attachments/assets/4151d00d-f171-4924-a5df-4a3ec343f029" />

---

## 2. Résumé exécutif

L'analyse statique de l'application **UnCrackable Level 2** révèle un niveau de risque **moyen** avec un score de **65/100**. Les principales vulnérabilités concernent la compatibilité avec des versions Android vulnérables (minSdk=19), la sauvegarde ADB activée, des privilèges root potentiels, et des protections insuffisantes dans la bibliothèque native.

<img width="1918" height="985" alt="image" src="https://github.com/user-attachments/assets/ffb894a8-14cf-460f-b35e-6e13261c84c1" />

---

## 3. Vulnérabilités identifiées

### 🔴 Vulnérabilité 1 — Compatible avec Android vulnérable (minSdk=19)
- **Sévérité** : HIGH
- **Référence MASVS** : MASVS-PLATFORM-1
- **Description** : L'application peut être installée sur Android 4.4 (minSdk=19), une version qui ne reçoit plus de mises à jour de sécurité.
- **Localisation** : `AndroidManifest.xml`
- **Impact** : Les utilisateurs sont exposés à des vulnérabilités non corrigées.
- **Recommandation** : Augmenter le `minSdkVersion` à 29 minimum.

<img width="1440" height="857" alt="image" src="https://github.com/user-attachments/assets/10c53da5-8203-4203-81e7-1e6890718dd6" />

---

### 🟠 Vulnérabilité 2 — Sauvegarde ADB activée
- **Sévérité** : WARNING
- **Référence MASVS** : MASVS-STORAGE-1
- **Description** : `android:allowBackup=true` permet d'extraire les données via ADB.
- **Localisation** : `AndroidManifest.xml`
- **Impact** : Un attaquant avec accès USB peut copier toutes les données de l'application.
- **Recommandation** : Mettre `android:allowBackup="false"`.

<img width="954" height="1143" alt="image" src="https://github.com/user-attachments/assets/e2401356-f032-4629-843f-63ea49119204" />

---

### 🟠 Vulnérabilité 3 — Demande de privilèges root
- **Sévérité** : WARNING
- **Référence MASVS** : MSTG-RESILIENCE-1
- **Description** : L'application peut demander des privilèges Super User.
- **Localisation** : `sg/vantagepoint/a/b.java`
- **Standard** : CWE-250
- **Impact** : Exécution avec des privilèges non nécessaires.
- **Recommandation** : Supprimer toute demande de privilèges root.

<img width="1906" height="926" alt="image" src="https://github.com/user-attachments/assets/4952abb7-e80a-45e3-a10d-5b02b03da9ae" />

---

### 🟡 Vulnérabilité 4 — Bibliothèque native sans fonctions fortifiées
- **Sévérité** : WARNING
- **Référence MASVS** : MASVS-RESILIENCE-2
- **Description** : `x86/libfoo.so` n'a pas de fonctions fortifiées (FORTIFY=False).
- **Localisation** : `lib/x86/libfoo.so`
- **Impact** : Vulnérable aux attaques buffer overflow.
- **Recommandation** : Recompiler avec `-D_FORTIFY_SOURCE=2`.

<img width="1916" height="735" alt="image" src="https://github.com/user-attachments/assets/eaec7f29-e002-40cb-a116-0e9e688ff11c" />

---

### 🟡 Vulnérabilité 5 — Symboles de débogage non supprimés
- **Sévérité** : WARNING
- **Référence MASVS** : MASVS-RESILIENCE-3
- **Description** : Les symboles de débogage sont disponibles dans `libfoo.so`.
- **Localisation** : `lib/x86/libfoo.so`
- **Impact** : Facilite la rétro-ingénierie de l'application.
- **Recommandation** : Supprimer les symboles avec `strip` à la compilation.

<img width="957" height="1147" alt="image" src="https://github.com/user-attachments/assets/2cc122b3-1f31-4b05-b77c-13189e7b3565" />

---

## 4. Recommandations prioritaires

1. **Augmenter minSdkVersion à 29** — Empêcher l'installation sur Android obsolète.
2. **Désactiver `android:allowBackup`** — Protéger les données utilisateur.
3. **Supprimer les demandes root** — Implémenter une détection de root.
4. **Recompiler avec `-D_FORTIFY_SOURCE=2`** — Protéger contre les buffer overflows.
5. **Supprimer les symboles debug** — Compliquer la rétro-ingénierie.

---

## 5. Corrélation OWASP MASVS

| Vulnérabilité | Référence MASVS | Catégorie |
|---|---|---|
| minSdk trop bas | MASVS-PLATFORM-1 | Platform |
| allowBackup activé | MASVS-STORAGE-1 | Storage |
| Privilèges root | MSTG-RESILIENCE-1 | Resilience |
| FORTIFY=False | MASVS-RESILIENCE-2 | Resilience |
| Symboles non supprimés | MASVS-RESILIENCE-3 | Resilience |

---

## 6. Annexes

### Composants exportés

<img width="1675" height="636" alt="image" src="https://github.com/user-attachments/assets/ae4deb01-0257-4b12-a36c-7075d21889e0" />

<img width="1633" height="727" alt="image" src="https://github.com/user-attachments/assets/b9e3630d-0429-44b1-b1b7-5d7856180454" />

<img width="1720" height="522" alt="image" src="https://github.com/user-attachments/assets/b31921f5-e1fb-47c8-ae3e-48561e24fbc9" />

<img width="1671" height="487" alt="image" src="https://github.com/user-attachments/assets/cc2df80d-4970-417a-966d-4e98a8a6db41" />

---

*Rapport généré dans le cadre du Lab 6 — Analyse statique d'applications Android avec MobSF*
