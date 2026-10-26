<!-- translation-of: decision-framework-for-ai-models.md @ 6618441f0600a1806c11da1cc766cf13e6a8a97c -->

# Un cadre décisionnel pour les modèles d'IA

Simon Kreis & Claude

Réalisé avec l'IA, principalement Claude (Anthropic), qui a contribué en partie ou en totalité au travail. Simon Kreis en est responsable. D'après une idée originale de Simon Kreis.

Mis à jour en octobre 2026. Les bancs d'essai et les versions changeront ; la méthode ne devrait pas changer. Sous licence [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

## Le problème : plus de choix, moins de clarté

Choisir un modèle était autrefois simple : on prenait le premier du classement. Cela ne fonctionne plus, pour plusieurs raisons qui se renforcent les unes les autres.

- **Trop de modèles, trop de bancs d'essai.** De nouveaux modèles sortent à quelques semaines d'intervalle, chacun avec sa propre série de bancs d'essai favorables. Les classements sont sans cesse rebattus, et aucun classement ne répond à lui seul à la question « quel modèle dois-je utiliser pour mon travail ? »
- **Quantitatif contre qualitatif.** Les bancs d'essai mesurent ce qui peut être noté : les mathématiques, le code, l'accomplissement des tâches. Ils peinent à saisir ce que les utilisateurs ressentent réellement : le ton, le jugement, la complaisance, la disposition à dire « je ne sais pas ». Certains bancs d'essai sont aussi contaminés, leurs questions de test ayant fuité dans les données d'entraînement, de sorte qu'un score élevé peut refléter de la mémorisation plutôt qu'une capacité.
- **Ressenti contre capacité mesurée.** Deux modèles peuvent afficher des scores semblables alors que les utilisateurs préfèrent nettement l'un des deux. Claude est l'exemple habituel : une grande part de sa réputation repose sur un « ressenti » que les scores ne saisissaient pas. Jusqu'à récemment, ce ressenti n'était mesuré que par des arènes de préférence collective comme LMArena et par des communautés d'admirateurs, deux sources qui portent de forts biais humains.
- **Le prix par jeton n'est pas le coût par tâche.** Un modèle aux jetons bon marché qui a besoin de trois fois plus de jetons pour finir une tâche n'est pas bon marché. L'efficacité en jetons compte pour une seconde raison : un modèle gourmand en jetons remplit plus vite sa propre fenêtre de contexte, et la qualité se dégrade à mesure que le contexte se remplit.
- **La vitesse par jeton n'est pas le temps par tâche.** Un modèle qui génère des jetons rapidement mais qui en exige beaucoup plus, ou beaucoup plus de tours, peut finir après un modèle plus lent mais plus économe.
- **Le harnais change tout.** Le même modèle se comporte différemment selon l'environnement d'agent, les outils et les prompts qui l'entourent, et la plupart des comparaisons n'en tiennent pas compte.

Il en résulte un paradoxe pour les utilisateurs avancés. Le paysage est trop complexe pour être suivi de près, et pourtant quiconque fait tourner des agents d'IA de manière intensive a toutes les raisons de rester à la pointe : le bon choix se capitalise en meilleur travail et en coûts plus bas, chaque jour. Ce qui manque, c'est un cadre décisionnel, une manière reproductible de transformer des bancs d'essai en un choix.

## Le cadre : choisir par phase, pas par classement

Les utilisateurs avancés d'agents ne commencent pas par lancer des agents. Ils travaillent en trois étapes :

1. **Orientation stratégique** : que cherchons-nous à accomplir, et pourquoi ? Remue-méninges, remise en question des hypothèses, cadrage de la décision.
2. **Spécifications** : comment, concrètement ? Le cahier des charges du projet, le plan du document, la spécification technique.
3. **Exécution** : des agents mettent en œuvre la spécification, souvent sur de nombreuses étapes et de nombreux jetons.

Ces étapes demandent des modèles différents, si bien que le choix du modèle se divise en deux phases.

**Phase 1 : stratégie et spécifications.** Il s'agit de raisonnement de haut niveau, en conversation. Ce qui compte le plus, ce sont l'intelligence et les traits de personnalité : la calibration, l'honnêteté, la capacité à contester une prémisse erronée. L'aptitude agentique compte moins. Le modèle peut être testé nu, avec un prompt système et sans harnais, par des appels directs à l'API ou par un service comme OpenRouter. Le volume est faible, quelques milliers de jetons par session, si bien que le prix compte à peine. C'est ici que les modèles de pointe coûteux se justifient.

**Phase 2 : exécution.** Il s'agit de travail agentique à l'intérieur d'un harnais, avec des outils et des fichiers, à fort volume. Ce qui compte, c'est l'accomplissement fiable des tâches à faible coût par tâche. La personnalité compte moins ici : les décisions ont été prises en phase 1, et un prompt système peut corriger la plupart des traits indésirables. C'est ici qu'un modèle moins cher situé sur la frontière coût-performance se justifie.

Un modèle moins cher est une façon d'atteindre cette frontière, pas la seule. Le plus récent modèle de pointe, utilisé à son réglage de raisonnement le plus bas, fait souvent aussi bien, voire mieux, qu'un modèle moins cher poussé à un réglage élevé, et les deux voies se trouvent fréquemment côte à côte sur la frontière du coût par tâche. Aucune n'est la règle : celle qui l'emporte dépend de la tâche et des prix du mois, et les bancs d'essai ci-dessous permettent de le savoir.

La suite de cet article montre quels bancs d'essai servent chaque phase. J'utilise [Artificial Analysis](https://artificialanalysis.ai/evaluations) comme source de référence : ses évaluations sont indépendantes, beaucoup reposent sur des jeux de données privés qui limitent la contamination, et plusieurs indiquent le coût par tâche plutôt que le prix par jeton.

## Phase 1 : choisir un partenaire de réflexion

### AA-PersonalityBench : le lire selon ce qu'un prompt ne peut pas corriger

[AA-PersonalityBench](https://artificialanalysis.ai/evaluations/aa-personalitybench) est la première tentative sérieuse de quantifier le « ressenti ». Il décompose la communication en 30 traits regroupés en 8 indices. Un panel de juges LLM compare deux réponses anonymisées à la même question, et les résultats deviennent des cotes Elo par trait.

La clé de lecture : le banc d'essai mesure le comportement **par défaut** de chaque modèle, sans prompt système. Certains traits sont faciles à changer par un prompt, et leurs scores ne décrivent qu'un réglage d'usine. D'autres dépendent de ce que le modèle sait et de son jugement, et aucun prompt ne peut les ajouter. Je répartis les 30 traits en trois groupes :

- **Capacités qu'un prompt ne peut pas ajouter :** Truthful, Constructive Pushback, Information Dense, Evidence-Based. Un prompt ne peut pas empêcher un modèle d'halluciner ce qu'il ne sait pas, ni lui faire détecter une prémisse erronée qu'il ne voit pas.
- **Partiellement corrigibles :** Admits Uncertainty, Sycophantic, Flags Nuances, Disclaims Assumptions, Creative, Pragmatic, Resource-Specific. Un prompt augmente la fréquence du comportement, pas sa présence aux bons endroits. Vous pouvez faire dire « je ne suis pas sûr » plus souvent à n'importe quel modèle ; seule la calibration le lui fait dire quand cela compte.
- **Dispositions qu'un prompt peut corriger :** tous les traits de style (éloquence, humour, chaleur), tous les traits de structure (réponse d'abord, facile à survoler, synthèse finale) et des postures comme Confident ou Opinionated.

Les deux premiers groupes devraient guider le choix. Le troisième, non, parce que le prompt système à la fin de cet article s'en charge.

Le classement personnalisé du banc d'essai permet de pondérer les traits de −2 à +2. Mes pondérations :

| Trait | Pondération |
|---|---|
| Truthful | +2 |
| Admits Uncertainty | +2 |
| Constructive Pushback | +2 |
| Sycophantic | −2 |
| Flags Nuances | +1 |
| Disclaims Assumptions | +1 |
| Evidence-Based | +1 |
| Information Dense | +1 |
| Pragmatic | +1 |
| Creative | +1 |

Tous les autres traits restent non sélectionnés.

### AA-Omniscience : vérifier la calibration

PersonalityBench mesure la fréquence à laquelle un modèle admet son incertitude, pas s'il le fait aux bons moments. Un modèle qui émet des réserves sur tout peut obtenir un bon score. [AA-Omniscience](https://artificialanalysis.ai/evaluations/omniscience) comble cette lacune : il récompense les bonnes réponses, pénalise les hallucinations et ne pénalise pas l'abstention. Autrement dit, il mesure si un modèle sait ce qu'il ne sait pas, ce qui est le trait le plus précieux de tous chez un partenaire de réflexion.

## Phase 2 : choisir un exécutant

Pour l'exécution, la question devient : quel modèle accomplit un vrai travail de manière fiable, au coût par tâche le plus bas ? Cherchez des bancs d'essai qui notent de vrais livrables, indiquent le coût par tâche et pénalisent les erreurs.

**Bureautique et travail intellectuel** (la priorité pour la plupart des travailleurs du savoir) :

- **[AA-Briefcase](https://artificialanalysis.ai/evaluations/aa-briefcase)** est la référence. Des agents mènent des flux de travail professionnels s'étendant sur plusieurs semaines et produisent des tableurs, des présentations et des notes de service, notés selon une grille d'évaluation, sur la qualité de l'analyse et sur la présentation. Il indique le coût par tâche, les jetons par tâche et le temps par tâche, et trace le score en fonction du coût par tâche, frontière de Pareto comprise. Choisissez parmi les modèles situés sur cette frontière.
- **[GDPval-AA](https://artificialanalysis.ai/evaluations/gdpval-aa)** est la contre-vérification : de vraies tâches dans 44 professions, jugées à l'aveugle. Il confirme qu'un modèle généralise au-delà des scénarios de Briefcase.
- **[AutomationBench-AA](https://artificialanalysis.ai/evaluations/automationbench-aa)** est le filtre de risque. Il pénalise les violations des garde-fous dans des applications professionnelles simulées, ce qu'il faut savoir avant de laisser un agent travailler sans supervision.
- Selon votre travail : **EnterpriseOps-Gym-AA** pour les agents qui modifient des systèmes d'entreprise à état, **AA-AnalystAgent** pour l'analyse de données, **APEX-Agents-AA** pour les tâches longues qui s'étendent sur plusieurs applications.

**Programmation** (secondaire pour la plupart des travailleurs du savoir) :

- **[Terminal-Bench 4.0](https://artificialanalysis.ai/evaluations/terminalbench-4-0)** mesure le travail technique agentique général dans un terminal.
- **[FrontierCode 1.1](https://artificialanalysis.ai/evaluations/frontiercode)** mesure la qualité du code selon qu'un mainteneur le fusionnerait, et pas seulement selon que les tests passent.

La règle de sélection est courte : **filtrer d'abord, classer ensuite.** Écartez les modèles qui hallucinent beaucoup ou se montrent complaisants, parce qu'un agent qui ne doute jamais exécutera une erreur jusqu'au bout. Classez ensuite les autres selon le coût par tâche de Briefcase, gardez ceux qui sont sur la frontière de Pareto, et confirmez avec GDPval-AA.

## Un modèle par phase, pas un routeur

Un raccourci tentant consiste à sauter le choix et à laisser un routeur choisir un modèle pour chaque requête. Les données vont dans l'autre sens. Le [Vals Router Index](https://www.vals.ai/benchmarks/vals_router_index) (octobre 2026) a testé cinq routeurs commerciaux sur environ 1 250 longues tâches agentiques en finance, en programmation, en droit et en fiscalité. Aucun ne se trouvait sur la frontière coût-précision : chacun était battu, à la fois sur le score et sur le coût, par au moins un modèle seul. Vals a aussi observé que les modèles faisaient souvent moins de travail sous un routeur, peut-être parce que les routeurs choisissent par défaut un effort de raisonnement plus faible.

Il existe une raison structurelle de s'y attendre, au-delà de ce que Vals a mesuré. Le travail agentique repose fortement sur la mise en cache des prompts : un long contexte réutilisé tour après tour est facturé à une fraction de son prix normal. Chaque fois qu'un routeur change de modèle, ce cache est perdu et le contexte entier est payé de nouveau. Un seul modèle par phase garde son cache chaud pendant toute la tâche.

Ce n'est pas en contradiction avec le cadre en deux phases. Un routeur décide automatiquement, requête par requête. Le cadre fait un choix délibéré une fois par phase, puis garde ce modèle fixe pendant toute la phase.

## Les bancs d'essai sont imparfaits, et restent ce que nous avons de mieux

Les réserves sont réelles. PersonalityBench utilise des juges LLM, qui ont leurs propres biais. Il évalue des réponses isolées, pas de longues conversations ni de boucles agentiques. Chaque banc d'essai cité ici sera révisé, renommé ou remplacé.

Rien de tout cela n'est une raison de se rabattre sur l'intuition. Une impression personnelle du ressenti d'un modèle ne peut pas tenir compte à la fois de la calibration, du coût par tâche, de l'efficacité en jetons et de la fiabilité. Les bancs d'essai indépendants, avec des jeux de données privés et des comparaisons à l'aveugle, restent la méthode la plus scientifique et la plus holistique dont on dispose. La réponse est de les lire avec un cadre, et de revoir le cadre à mesure que les bancs d'essai changent.

## Réduire l'écart : un prompt système pour les modèles moins chers

Le style et la structure ne devraient pas guider le choix du modèle, parce qu'un prompt les corrige. La même logique rend les modèles moins chers viables pour l'exécution : un bon prompt système les rapproche du comportement par défaut des modèles haut de gamme sur les traits qu'un prompt peut atteindre. Il n'ajoutera ni connaissances ni véritable calibration, et c'est pourquoi celles-ci sont des critères de sélection, mais il améliore de façon fiable la calibration dans la forme, la résistance à la flatterie, l'explicitation des hypothèses et la concision.

Un prompt peut aggraver les choses. Les règles qui tentent d'installer du jugement (« vise le vrai but », « corrige le cap ») ajoutent du poids sans ajouter de jugement : les modèles faibles les suivent à la lettre, les modèles forts deviennent rigides. Le prompt de base ci-dessous ne garde donc que les traits qu'un prompt peut atteindre, et il est destiné aux exécutants de la phase 2. Avant de l'adopter, faites cinq requêtes typiques avec et sans lui ; ne le gardez que s'il l'emporte.

```text
You are an executor for knowledge work. If these rules conflict, truth wins.

1. Calibrate. Separate what you know, infer and guess. Say "I don't know" when it is true. Never invent facts, sources or certainty.
2. Don't flatter. If a premise, plan or piece of data is wrong, say so first, with the reason. Hold your position unless you get a new argument or evidence.
3. Report honestly. Never say something is done, tested or working unless you verified it. Confirm before any irreversible or external action.
4. Be brief. Lead with the answer. No preamble, filler, praise or recap. Mention only the caveat that could change the decision.
```

Pour un partenaire de réflexion de pointe (phase 1), un prompt minimal suffit :

```text
- Answer first, briefly. Ask a question only when missing context would change the answer.
- If the user is wrong, say so plainly, with the reason.
- End with a concrete recommendation.
```

## Le cadre en un paragraphe

Réfléchissez avant d'exécuter, et choisissez vos modèles en conséquence. Pour la stratégie et les spécifications, prenez le modèle le plus intelligent qui a les meilleures capacités sur PersonalityBench et le plus faible taux d'hallucination sur Omniscience, et ne vous souciez pas du prix. Pour l'exécution, prenez un modèle situé sur la frontière de Pareto du coût par tâche d'AA-Briefcase, un modèle moins cher ou un modèle de pointe à faible effort, selon ce que favorise la frontière, après un filtrage sur la fiabilité, et donnez-lui un prompt de base limité à ce qu'un prompt peut corriger, gardé seulement s'il fait mieux que l'absence de prompt sur vos propres requêtes ; le partenaire de réflexion n'a besoin que d'un prompt minimal. Gardez un modèle par phase. Revoyez le choix quand les bancs d'essai bougent.

Pour la place de ce choix dans un système personnel de fichiers et d'agents, voir [*Le harnais autonome*](https://github.com/SimonKreis/self-contained-harness), partie IV.
