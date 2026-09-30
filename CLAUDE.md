@AGENTS.md

# IFSPA — Gestion de l'écolage

Application de l'**Institut de Formation Supérieur des Paramédicaux Atsinanana** (Toamasina, Madagascar) : suivi des étudiants de l'inscription à la sortie, et de l'écolage mois par mois. Landing publique + espace connecté.

Spec complète et feuille de route : [docs/PLAN.md](docs/PLAN.md). Cocher les tâches au fil de l'avancement.

## Stack

- Next.js 16 (App Router, Server Actions, Turbopack). `proxy.ts` remplace `middleware.ts` ; `params`, `searchParams`, `cookies()`, `headers()` sont **async**. Lire `node_modules/next/dist/docs/` en cas de doute.
- Tailwind v4 + shadcn/ui (style `radix-nova`). Formulaires : composant `field` + react-hook-form + zod (le composant `form` n'existe plus).
- Prisma 7 : client généré dans `src/generated/prisma` (import `@/generated/prisma/client`), adaptateur `@prisma/adapter-pg`, config dans `prisma.config.ts`. `migrate dev` ne régénère pas le client : lancer `npx prisma generate`.
- Better Auth (email + mot de passe, plugin `admin`, inscription publique désactivée).
- PostgreSQL 17 dans Docker (port **5434**). App en dev sur le port **3100**.
- **Production : cPanel (Node.js App + PostgreSQL), sans Docker. Docker uniquement pour Postgres en dev.** Build `output: "standalone"` fait en local, déployé par SSH/SFTP, puis `prisma migrate deploy` (voir phase 8 de PLAN.md).

## Commandes

```bash
npm run db:up          # démarre Postgres (docker compose)
npm run db:migrate     # prisma migrate dev
npx prisma generate    # après chaque changement de schéma
npm run db:seed        # admin + filières IF/SF + année 2026-2027 + tarifs d'exemple
npm run dev            # http://localhost:3100
npm test               # vitest (tests/)
npm run lint && npm run typecheck && npm run build   # avant de clore une phase
```

Admin de dev : identifiants `SEED_ADMIN_*` dans `.env`.

## Règles métier (ne pas enfreindre)

- **Matricule** `{CODE_FILIERE}-{ANNEE_ENTREE}-{NNNN}` (ex. `IF-2026-0001`), séquence par filière et année d'entrée, via la table `Compteur` (incrément atomique en transaction). **Ne change jamais**, même en cas de redoublement.
- **Filières** : `IF` Infirmier(e)s, `SF` Sage-femmes. Durée configurable par filière.
- **Écolage** : droits d'inscription annuels + mensualités (10 mois, octobre→juillet par défaut). Les `Echeance` sont générées à l'inscription et **copient** le montant du `Tarif` (un changement de tarif ne réécrit pas le passé). Paiements répartis de l'échéance la plus ancienne à la plus récente. **Pas de notion d'« arriérés »** (retirée à la demande du client) : chaque année scolaire est suivie pour elle-même, ses impayés restent dus sur cette année, rien n'est « reporté ».
- **Reçu = ticket d'imprimante thermique 80 mm** (option 58 mm), monochrome, jamais A4/A5. Seul le relevé d'écolage est en A4.
- **Montants** en ariary, entiers (`Int`), jamais de décimales.
- **Aucune suppression physique de paiement** : l'admin l'annule (`annule`, motif, auteur, date). Reçu numéroté `R-{ANNEE}-{NNNNN}`.
- **Admissions en lot** : transaction ; l'annulation (admin uniquement) est bloquée si des paiements existent sur les inscriptions créées.
- **Rôles** : `admin` et `user`. `user` ne supprime ni n'annule rien, n'accède pas aux Paramètres. **Vérifier le rôle côté serveur** dans chaque Server Action / route (`requireRole`), pas seulement en masquant les boutons.
- **Pas de journal d'audit** (retiré à la demande du client). Les annulations restent tracées sur l'objet lui-même (`annulePar`, `dateAnnulation`, motif).

## Conventions

- Interface, messages et noms métier en **français** (modèles Prisma : `Etudiant`, `Paiement`…). Code technique en anglais toléré.
- Logique métier dans des fonctions pures testables : `src/lib/{matricule,ecolage,montant-en-lettres}.ts`, tests dans `tests/`.
- Accès base uniquement côté serveur via `db` de `src/lib/db.ts` (`server-only`).
- Mutations dans `src/server/actions/*.ts` : valider avec zod, vérifier le rôle, puis `revalidatePath`/`refresh`.
- Couleurs uniquement via les tokens CSS (`--primary`, `--accent`…) ; couleurs de statut (payé/partiel/retard) distinctes du rose d'accent.
