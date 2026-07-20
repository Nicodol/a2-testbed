# a2-testbed

Second banc d'essai pour [Cronwake](https://cronwake.vercel.app). Chaque workflow est concu pour
declencher UN comportement precis de Cronwake, pour pouvoir le verifier a la main. Repo public :
les GitHub Actions y sont gratuites, et les crons frequents sont volontairement instables (GitHub
les sous-execute), c'est voulu.

Pour tester : installer la GitHub App Cronwake sur ce repo, puis regarder chaque monitor dans le
dashboard et les alertes dans le canal configure.

| Workflow | Ce qu'il fait | Pourquoi il est la |
|---|---|---|
| `nominal-hourly` | Cron horaire normal qui reussit | Reference "ok", pour comparer avec les cas casses |
| `every-minute` | Demande un run chaque minute | GitHub le bride : montre les runs manques et le faible taux de presence |
| `always-fails` | Part a l'heure mais finit toujours en echec | Tester l'alerte "run en echec" (opt-in), distincte d'un run manque |
| `flaky` | Echoue une fois sur deux | Tester le seuil d'echecs CONSECUTIFS et le message "runs passing again" |
| `slow` | Tourne 3 minutes | Tester l'alerte "slow-job" (opt-in), regler un seuil sous 3 min |
| `disable-me` | A desactiver a la main dans l'onglet Actions | Tester la detection du "silencieusement desactive" |
| `daily-dispatchable` | Quotidien + bouton "Run workflow" | Tester un job quotidien et provoquer un run a la demande |
| `weekly` | Cron rare (lundi 9h) | Tester la couverture "jour zero" (surveille avant son 1er run) |
| `timezone-paris` | Cron a 8h heure de Paris | Tester la gestion du fuseau horaire (feature GitHub de mars 2026) |
| `multi-schedule` | Un workflow, deux crons | Tester plusieurs horaires sur un meme job |
| `drops-its-cron` | Cron que tu retireras plus tard | Tester l'alerte "monitoring stopped: cron removed" quand un cron disparait |
| `push-only` | Se declenche sur push, pas de cron | Verifier que Cronwake IGNORE les workflows non planifies |

## Scenarios manuels a jouer

- **Desactivation silencieuse** : onglet Actions -> `disable-me` -> bouton "Disable workflow". Cronwake
  doit alerter "disabled".
- **Cron retire** : editer `drops-its-cron.yml`, supprimer les 2 lignes `schedule:` / `- cron:`, garder
  `workflow_dispatch`. Cronwake doit envoyer UNE alerte d'arret de surveillance, pas une fausse "missed".
- **Retour au vert** : laisser `always-fails` alerter, puis le corriger (remplacer `exit 1` par `exit 0`)
  et pousser : Cronwake doit envoyer "runs passing again".
