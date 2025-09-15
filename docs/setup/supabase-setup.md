# ⚡ Configuration Supabase pour Emoji Code Mood

## 🚀 Pourquoi Supabase ?

Supabase est une alternative moderne à Firebase qui offre :
- ✅ **Configuration plus simple** (5 minutes vs 15 minutes)
- ✅ **Base de données PostgreSQL** familière aux développeurs
- ✅ **Real-time** natif avec WebSockets
- ✅ **Interface d'administration** intuitive
- ✅ **Quotas gratuits généreux** (500MB, 2GB de bande passante)
- ✅ **Open source** et auto-hébergeable

## 📋 Configuration Étape par Étape

### 1. Création du Projet Supabase

1. **Allez** sur [supabase.com](https://supabase.com)
2. **Créez un compte** (GitHub recommandé)
3. **Nouveau projet** :
   - **Nom** : `emoji-code-mood-[votre-nom]`
   - **Mot de passe DB** : Générer automatiquement (⚠️ sauvegardez-le !)
   - **Région** : `West EU (Ireland)` pour l'Europe
4. **Création** : ~2 minutes d'attente

### 2. Configuration de la Base de Données

#### Création de la Table avec Structure Française

1. **SQL Editor** dans le menu latéral
2. **Nouveau query** et collez ce code **EXACT** :

```sql
-- Création de la table humeur (structure française complète)
CREATE TABLE public.humeur (
  id BIGSERIAL PRIMARY KEY,
  nom TEXT NOT NULL CHECK (length(nom) >= 2 AND length(nom) <= 30),
  emoji TEXT NOT NULL CHECK (length(emoji) >= 1 AND length(emoji) <= 10),
  langage_prefere TEXT NOT NULL CHECK (
    langage_prefere = ANY (ARRAY[
      'javascript', 'typescript', 'python', 'java', 'csharp',
      'php', 'cpp', 'rust', 'go', 'kotlin', 'swift', 'ruby'
    ])
  ),
  autre_preference TEXT NOT NULL CHECK (
    autre_preference = ANY (ARRAY[
      'jeux-video', 'streaming', 'youtube', 'twitch', 'design', 'photoshop',
      'video-editing', 'ui-ux', 'musique', 'spotify', 'production-musicale',
      'podcasts', 'intelligence-artificielle', 'chatgpt', 'robotique',
      'blockchain', 'apps-mobiles', 'tiktok', 'instagram', 'snapchat',
      'sport', 'fitness', 'course', 'velo', 'netflix', 'series',
      'cinema', 'disney', 'lecture', 'cours-en-ligne', 'langues',
      'tutoriels', 'cuisine', 'voyage', 'shopping', 'nature'
    ])
  ),
  commentaire TEXT CHECK ((commentaire IS NULL) OR (length(commentaire) <= 100)),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Colonne calculée pour rétrocompatibilité
ALTER TABLE public.humeur ADD COLUMN langage TEXT GENERATED ALWAYS AS (langage_prefere) STORED;

-- Index pour améliorer les performances
CREATE INDEX IF NOT EXISTS idx_humeur_created_at ON public.humeur (created_at DESC);
CREATE INDEX IF NOT EXISTS idx_humeur_langage_prefere ON public.humeur (langage_prefere);
CREATE INDEX IF NOT EXISTS idx_humeur_autre_preference ON public.humeur (autre_preference);

-- Activer Row Level Security (sécurité)
ALTER TABLE public.humeur ENABLE ROW LEVEL SECURITY;

-- Politique : lecture publique (formation ouverte)
CREATE POLICY "Lecture publique des humeurs" 
ON public.humeur FOR SELECT 
TO public 
USING (true);

-- Politique : insertion publique avec validation complète
CREATE POLICY "Insertion contrôlée des humeurs" 
ON public.humeur FOR INSERT 
TO public 
WITH CHECK (
  -- Validation des champs obligatoires
  nom IS NOT NULL AND length(nom) BETWEEN 2 AND 30 AND
  emoji IS NOT NULL AND length(emoji) BETWEEN 1 AND 10 AND
  langage_prefere IS NOT NULL AND
  autre_preference IS NOT NULL AND
  -- Limitation du commentaire
  (commentaire IS NULL OR length(commentaire) <= 100)
);

-- Politique : suppression pour maintenance (enseignants)
CREATE POLICY "Suppression pour maintenance" 
ON public.humeur FOR DELETE 
TO public 
USING (true);

-- Politique : mise à jour limitée (correction de typos)
CREATE POLICY "Modification limitée" 
ON public.humeur FOR UPDATE 
TO public 
USING (created_at > NOW() - INTERVAL '5 minutes')
WITH CHECK (
  -- Ne permettre que la modification du commentaire
  nom = OLD.nom AND
  emoji = OLD.emoji AND
  langage_prefere = OLD.langage_prefere AND
  autre_preference = OLD.autre_preference AND
  created_at = OLD.created_at
);

-- Activer les changements temps réel
ALTER PUBLICATION supabase_realtime ADD TABLE public.humeur;
```

3. **Exécutez** le script (bouton `Run`)

#### Vérification

1. **Table Editor** > `humeur`
2. La table doit apparaître avec les colonnes : `id`, `nom`, `emoji`, `langage_prefere`, `autre_preference`, `commentaire`, `created_at`, `langage`

### 3. Configuration de l'Application

#### Récupération des Clés

1. **Settings** > **API**
2. **Copiez** ces informations :
   - **URL** : `https://xxxxxxxxxxx.supabase.co`
   - **anon public** key : `eyJhbGciOiJIUzI1NiIs...`

#### Configuration GitHub Secrets

1. **Dans votre repository GitHub**, allez dans **Settings → Secrets and variables → Actions**
2. **Ajoutez ces secrets :**
   - Name: `SUPABASE_URL`, Secret: Votre URL Supabase
   - Name: `SUPABASE_ANON_KEY`, Secret: Votre clé anonyme

3. **Commitez un changement** pour déclencher le redéploiement

## 🧪 Test de la Configuration

### Test Immédiat

1. **Ouvrez** votre application GitHub Pages
2. **Statut** doit afficher : "✅ Connecté - Synchronisation temps réel active"
3. **Formulaire** : Testez avec prénom + emoji + langage + préférence

### Test Multi-Utilisateurs

1. **Ouvrez** l'app sur 2 appareils différents
2. **Ajoutez** une humeur sur un appareil
3. **Vérifiez** qu'elle apparaît instantanément sur l'autre
4. **Animation** d'arrivée des nouvelles humeurs

### Vérification Base de Données

1. **Supabase Dashboard** > **Table Editor** > `humeur`
2. Les données doivent apparaître immédiatement avec :
   - `nom` : Prénom de l'étudiant
   - `emoji` : Humeur sélectionnée
   - `langage_prefere` : Langage choisi (valeur technique)
   - `autre_preference` : Préférence choisie (valeur technique)
   - `commentaire` : Message optionnel
   - `created_at` : Timestamp automatique
   - `langage` : Copie de `langage_prefere` (colonne calculée)

## 🔧 Configuration Avancée

### Règles de Sécurité Personnalisées

Pour limiter l'accès par horaires de cours :

```sql
-- Politique horaire (cours de 8h à 18h, semaine seulement)
CREATE POLICY "Horaires de cours" 
ON public.humeur FOR INSERT 
TO public 
WITH CHECK (
  EXTRACT(hour FROM NOW()) >= 8 AND 
  EXTRACT(hour FROM NOW()) <= 18 AND
  EXTRACT(dow FROM NOW()) BETWEEN 1 AND 5  -- Lundi à vendredi
);
```

### Anti-spam Avancé

```sql
-- Politique anti-doublon (empêche les humeurs identiques en 5 minutes)
CREATE POLICY "Anti-doublon" 
ON public.humeur FOR INSERT 
TO public 
WITH CHECK (
  NOT EXISTS (
    SELECT 1 FROM public.humeur 
    WHERE nom = NEW.nom
    AND emoji = NEW.emoji 
    AND langage_prefere = NEW.langage_prefere
    AND autre_preference = NEW.autre_preference
    AND created_at > NOW() - INTERVAL '5 minutes'
  )
);
```

### Limitation par Session de Cours

```sql
-- Table pour les sessions de cours
CREATE TABLE public.sessions_cours (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  nom TEXT NOT NULL,
  classe TEXT,
  enseignant TEXT,
  debut TIMESTAMPTZ DEFAULT NOW(),
  fin TIMESTAMPTZ,
  active BOOLEAN DEFAULT true,
  max_participants INTEGER DEFAULT 50
);

-- Lier les humeurs aux sessions
ALTER TABLE public.humeur ADD COLUMN session_id UUID REFERENCES public.sessions_cours(id);

-- Politique par session
CREATE POLICY "Limitation par session" 
ON public.humeur FOR INSERT 
TO public 
WITH CHECK (
  session_id IN (
    SELECT id FROM public.sessions_cours 
    WHERE active = true 
    AND debut <= NOW() 
    AND (fin IS NULL OR fin >= NOW())
  )
);
```

## 📊 Analytics pour Enseignants

### Requêtes Utiles

```sql
-- Participation par classe/session
SELECT 
  DATE(created_at) as jour,
  COUNT(*) as nb_participants,
  COUNT(DISTINCT nom) as nb_uniques
FROM public.humeur
WHERE created_at >= CURRENT_DATE - INTERVAL '7 days'
GROUP BY jour
ORDER BY jour DESC;

-- Top des langages préférés
SELECT 
  langage_prefere,
  COUNT(*) as count,
  ROUND(COUNT(*)::numeric / SUM(COUNT(*)) OVER() * 100, 1) as pourcentage
FROM public.humeur
GROUP BY langage_prefere
ORDER BY count DESC;

-- Répartition des préférences
SELECT 
  autre_preference,
  COUNT(*) as count
FROM public.humeur
GROUP BY autre_preference
ORDER BY count DESC
LIMIT 10;

-- Analyse des humeurs par période
SELECT 
  EXTRACT(hour FROM created_at) as heure,
  emoji,
  COUNT(*) as frequence
FROM public.humeur
WHERE DATE(created_at) = CURRENT_DATE
GROUP BY heure, emoji
ORDER BY heure, frequence DESC;
```

### Dashboard Personnalisé

Créez des vues pour suivre :
- **Évolution** de la participation dans le temps
- **Langages populaires** par promotion/classe
- **Tendances** des préférences tech
- **Moments** de forte activité
- **Diversité** des profils étudiants

## 🚨 Dépannage

### ❌ "Failed to fetch"

**Causes possibles :**
- URL Supabase incorrecte
- Clé API incorrecte  
- Projet Supabase en pause

**Solutions :**
1. Vérifiez `SUPABASE_URL` et `SUPABASE_ANON_KEY` dans GitHub Secrets
2. Projet Supabase : `Settings` > `General` > Vérifiez le statut
3. Quotas : `Settings` > `Usage` > Vérifiez les limites

### ❌ "Table 'humeur' does not exist"

**Diagnostic :**
```sql
-- Vérifiez l'existence de la table
SELECT table_name FROM information_schema.tables 
WHERE table_schema = 'public' AND table_name = 'humeur';
```

**Solution :**
- Re-exécutez le script de création complet
- Vérifiez que vous utilisez bien `humeur` et non `moods`

### ❌ "Row Level Security policy violation"

**Diagnostic :**
```sql
-- Vérifiez les politiques
SELECT * FROM pg_policies WHERE tablename = 'humeur';
```

**Solution :**
```sql
-- Recréez les politiques de base
DROP POLICY IF EXISTS "Lecture publique des humeurs" ON public.humeur;
DROP POLICY IF EXISTS "Insertion contrôlée des humeurs" ON public.humeur;

CREATE POLICY "Enable read access for all users" ON public.humeur
  FOR SELECT USING (true);

CREATE POLICY "Enable insert for all users" ON public.humeur
  FOR INSERT WITH CHECK (true);
```

### ❌ Pas de Temps Réel

**Vérifications :**
1. **Realtime** activé : `Database` > `Replication` > table `humeur` activée
2. **Publications** : Vérifiez que `supabase_realtime` inclut la table
3. **Console navigateur** : Vérifiez les erreurs WebSocket (F12)

**Solution :**
```sql
-- Réactiver la réplication temps réel
ALTER PUBLICATION supabase_realtime ADD TABLE public.humeur;
```

## 🔄 Maintenance

### Sauvegarde des Données

```bash
# Export des données (depuis votre machine avec CLI Supabase)
npx supabase db dump --data-only > backup-humeurs.sql
```

### Nettoyage Automatique

```sql
-- Fonction pour supprimer les humeurs anciennes (> 30 jours)
CREATE OR REPLACE FUNCTION nettoyer_humeurs_anciennes()
RETURNS void AS $$
BEGIN
  DELETE FROM public.humeur 
  WHERE created_at < NOW() - INTERVAL '30 days';
  
  RAISE NOTICE 'Nettoyage terminé. Humeurs supprimées : %', ROW_COUNT;
END;
$$ LANGUAGE plpgsql;

-- Exécution manuelle
-- SELECT nettoyer_humeurs_anciennes();
```

### Monitoring

1. **Dashboard Supabase** > `Settings` > `Usage`
2. Surveillez :
   - **Database size** (500MB max gratuit)
   - **Bandwidth** (2GB max gratuit) 
   - **API requests** (500K max gratuit)
   - **Realtime connections** (500 max simultanées)

## 🌍 Configuration Multi-Classes

### Structure Recommandée

```sql
-- Table des classes/groupes
CREATE TABLE public.classes (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  nom TEXT UNIQUE NOT NULL,
  annee_scolaire TEXT,
  enseignant_email TEXT,
  code_acces TEXT UNIQUE, -- Pour limiter l'accès
  active BOOLEAN DEFAULT true,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Lier les humeurs aux classes
ALTER TABLE public.humeur ADD COLUMN classe_id UUID REFERENCES public.classes(id);

-- Politique d'isolation par classe
CREATE POLICY "Isolation par classe" 
ON public.humeur 
FOR SELECT
USING (
  classe_id IN (
    SELECT id FROM public.classes 
    WHERE active = true
  )
);
```

## ✅ Checklist de Configuration

- [ ] Projet Supabase créé avec région appropriée
- [ ] Table `humeur` créée avec structure française complète
- [ ] Row Level Security activé avec politiques appropriées
- [ ] Realtime activé sur la table `humeur`
- [ ] URL et clé API ajoutées aux GitHub Secrets
- [ ] Application redéployée automatiquement
- [ ] Test multi-appareils réussi
- [ ] Dashboard Supabase vérifié
- [ ] Quotas surveillés
- [ ] Politiques de sécurité testées

## 🎉 Configuration Terminée !

Votre **brise-glace interactif** avec Supabase est maintenant opérationnel avec la structure française complète !

### 🏆 Ce que vous avez mis en place :

**✅ Base de données robuste :**
- Table `humeur` avec champs français (`nom`, `langage_prefere`, `autre_preference`)
- Contraintes de validation automatiques
- Index optimisés pour les performances
- Politiques de sécurité RLS configurées

**✅ Synchronisation temps réel :**
- WebSocket natif Supabase activé
- Affichage instantané des nouvelles humeurs
- Support multi-utilisateurs simultanés

**✅ Sécurité et validation :**
- Row Level Security (RLS) activé
- Validation des données côté base
- Anti-spam intégré
- Politiques d'accès granulaires

**✅ Monitoring et maintenance :**
- Dashboard administrateur intégré
- Requêtes d'analyse prêtes
- Procédures de sauvegarde
- Système de nettoyage automatique

### 🎯 Utilisation en cours

Votre brise-glace est maintenant prêt pour :

1. **Débuter un cours** : Les étudiants partagent leur humeur et leurs préférences
2. **Faire connaissance** : Découvrir les profils tech de la classe
3. **Animer les sessions** : Voir l'évolution des humeurs en temps réel
4. **Analyser les tendances** : Comprendre les préférences de vos étudiants

### 📈 Données collectées

Chaque participation génère :
```sql
{
  "nom": "Alex",
  "emoji": "🚀", 
  "langage_prefere": "javascript",
  "autre_preference": "intelligence-artificielle",
  "commentaire": "Motivé pour apprendre !",
  "created_at": "2025-01-15T09:30:00Z"
}
```

### 🔄 Prochaines étapes

1. **Testez avec vos étudiants** lors du prochain cours
2. **Analysez les résultats** via le dashboard Supabase
3. **Personnalisez l'interface** selon vos besoins (Module 02)
4. **Explorez les données** avec les requêtes SQL fournies

### 🆘 Support technique

En cas de problème :
1. Consultez la section **🚨 Dépannage** ci-dessus
2. Vérifiez les logs dans **GitHub Actions**
3. Examinez la console navigateur (F12)
4. Utilisez les requêtes de diagnostic fournies

### 🌟 Fonctionnalités avancées disponibles

- **Multi-classes** : Séparez les sessions par groupe
- **Analytics avancés** : Tableaux de bord personnalisés  
- **Export des données** : CSV, JSON pour analyse
- **Horaires restreints** : Limitez l'accès aux heures de cours
- **Sessions temporaires** : Créez des sessions limitées dans le temps

**🎭 Votre outil de brise-glace programmation est opérationnel !**

---

## 🔗 Liens utiles

- **Votre application** : `https://[votre-nom].github.io/emoji-code-mood/`
- **Dashboard Supabase** : [app.supabase.com](https://app.supabase.com)
- **Repository GitHub** : Votre fork du projet
- **Documentation Supabase** : [supabase.com/docs](https://supabase.com/docs)

*Prêt à transformer vos cours de programmation ! 🚀*
