Planning Document

Community

I chose online football (soccer) communities, including discussion forums and Reddit communities centered around football news, transfers, players, clubs, and international competitions.

This community is a good fit for text classification because discussion ranges from factual reporting and transfer news to emotional reactions, tactical analysis, and strong opinions. Football discussions frequently contain disagreements and varying levels of reasoning, making label boundaries meaningful and challenging.

⸻

Community Description

Football communities contain a mix of news sharing, tactical discussion, fan reactions, and opinionated debate. Distinguishing between these forms of discourse is useful because not all contributions serve the same purpose. Some comments provide information, some explain ideas, some express emotions, and others offer unsupported opinions.

⸻

Labels

NEWS

Definition: Comments whose primary purpose is to share factual information, statistics, reports, announcements, quotes, or updates.

Examples:

* “[Fabrizio Romano] Liverpool are prepared to bid again for Yan Diomandé.”
* “Mbappé has officially been ruled out of tomorrow’s match.”

Uncertain case:

* “Ronaldo has scored 15 goals this season, proving he’s still elite.”

⸻

ANALYSIS

Definition: Comments that explain, compare, reason, provide evidence, discuss tactics, or make cause-and-effect arguments.

Examples:

* “Portugal are playing for Ronaldo instead of playing football.”
* “The midfield struggles because the team loses numerical superiority during transitions.”

Uncertain case:

* “France remain favorites whenever Mbappé plays.”

⸻

HOT_TAKE

Definition: Strong opinions or judgments that contain little or no supporting reasoning.

Examples:

* “Ronaldo should have retired after Qatar.”
* “This is the worst Manchester United team ever.”

Uncertain case:

* “Henry is absolutely right.”

⸻

REACTION

Definition: Emotional responses, celebrations, disappointment, surprise, jokes, memes, or other low-information reactions.

Examples:

* “HE DID IT AGAIN!”
* “No way this actually happened.”

Uncertain case:

* “Ronaldo WC 2030. Here we go!”

⸻

Hard Edge Cases

Analysis vs Hot Take

Many football comments contain both an opinion and a small amount of reasoning.

Rule used:

If the comment primarily explains why something happened, label it ANALYSIS.

If the comment mainly expresses a judgment without meaningful explanation, label it HOT_TAKE.

Example:

“France remain favorites whenever Mbappé plays.”

Decision: HOT_TAKE because it expresses an opinion without supporting evidence.

⸻

Reaction vs Hot Take

Short comments sometimes express both emotion and opinion.

Rule used:

If the main purpose is emotional expression, label it REACTION.

If the main purpose is evaluating a player, team, or event, label it HOT_TAKE.

Example:

“Henry is absolutely right.”

Decision: HOT_TAKE because it endorses an opinion rather than expressing emotion.

⸻

Reaction vs Analysis

Some comments are extremely short but imply reasoning.

Example:

“Which raises the question of who hosts 2038.”

Decision: ANALYSIS because the comment advances a discussion rather than reacting emotionally.

⸻

Data Collection Plan

Examples were collected from football-related online discussions and Reddit communities.

Target distribution:

* NEWS: 50 examples
* ANALYSIS: 50 examples
* HOT_TAKE: 50 examples
* REACTION: 50 examples

Actual dataset size: 202 examples.

Final distribution:

* ANALYSIS: 74
* NEWS: 61
* HOT_TAKE: 42
* REACTION: 25

No label exceeded 70% of the dataset, so class imbalance remained manageable.

If a label became underrepresented during collection, additional examples were gathered specifically for that category.

⸻

Evaluation Metrics

Accuracy was used as an overall performance measure.

However, accuracy alone is insufficient because some labels occur more frequently than others.

Additional metrics:

* Precision
* Recall
* F1-score
* Confusion Matrix

These metrics help identify which label pairs are frequently confused and whether the model performs consistently across all classes.

⸻

Definition of Success

A useful classifier should achieve:

* Accuracy above 60%
* Reasonable performance across all four labels
* Clear separation between NEWS and non-NEWS categories

Because football discourse contains substantial ambiguity, perfect classification is unrealistic. A model that consistently identifies the major discourse type of a comment would be considered successful.

⸻

AI Tool Plan

Label Stress-Testing

Before annotation, I used ChatGPT to generate borderline examples between labels such as ANALYSIS vs HOT_TAKE and REACTION vs HOT_TAKE. These examples helped refine label definitions before collecting the dataset.

Annotation Assistance

I chose not to use AI-generated labels during annotation. All examples were reviewed and labeled manually using the definitions established in this document.

Failure Analysis

After evaluation, I planned to use ChatGPT to help identify recurring patterns in misclassified examples. Any patterns suggested by the AI would be verified manually by reviewing the actual predictions and confusion matrix before being included in the final report.

⸻

Reflection After Annotation

The most difficult boundary was between ANALYSIS and HOT_TAKE. Many football comments contain both an opinion and a small amount of reasoning.

Another challenge was distinguishing REACTION from short opinion statements. Very short comments often provide little context, making them difficult to classify consistently.

These challenges later appeared in the model evaluation results and became the primary source of classification errors.