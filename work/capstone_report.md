# Before You Refresh

## A client-wide check for a page review queue

- **Author:** Finley Septhian Darma
- **Lane:** Refresh / Content Opportunity Scoring, with a client-wide review check
- **Date:** 19 October 2026
- **Repository:** https://github.com/Fin1806/flyrank-ml-internship
- **Notebook:** https://github.com/Fin1806/flyrank-ml-internship/blob/main/work/notebooks/capstone.ipynb

## Abstract

When many pages for one client lose search visibility together, should an editor check the broader pattern before refreshing individual pages? I use the gated FlyRank warehouse release to compare May search signals with observed June declines and to examine complete May–June page windows by client. On the held-out clients, the simple rule reaches 95% precision among its top 20 pages, compared with 80% for a logistic model, against a 65.5% decline base rate. A separate end-of-June check flags 14 of 32 clients with enough matched pages under the stated broad-drop rule. These observations support a human investigation step before page-level edits, without identifying a decline's cause or measuring refresh effects.

## Introduction and problem

An editor may receive a queue of pages whose search performance looks weak. A page edit could be useful, but a drop across many pages for the same client raises a different question: is there a shared problem to investigate first? Google Search Central recommends checking whether a traffic drop affects a whole site, a group of pages, or one page. This study tests a small, measurable client-level signal that can sit before the existing page queue. It is an independent internship analysis, not a diagnosis of a client's site.

The output is a review route for an editor: check coverage, investigate a broader pattern, or review page evidence. The cost of a wrong call is wasted editorial effort or delayed attention to a shared issue. Every route still requires a person to decide what to do.

## Data

The source is the gated FlyRank Internship warehouse release v20260703. The full release contains 78,835,655 daily rows in fact_content_daily_performance. This analysis uses only its May and June 2026 partitions, not all 78.8 million rows. The Week 3 source audit counted 23,017,813 daily rows in those windows and 6,390 repeated GSC rows. The repeated daily keys had matching GSC values and were deduplicated before aggregation.

The prediction check uses May 2–31 as the 30-day feature window and June 1–30 as the 30-day outcome window. A historical page needs all 60 daily observations, nonmissing impressions and clicks, and at least 100 May impressions. That leaves 102,532 page-client records across 42 clients. The June review queue separately needs 30 June days and at least 100 June impressions; it contains 96,678 pages across 44 clients.

The broad-drop check uses the historical complete-window pages, including those that fell below 100 June impressions. Requiring 100 June impressions here would remove some of the strongest declines. At least 30 matched pages are needed per client for a flag, leaving 32 eligible clients. The 10 historical clients below that count stay unclassified.

Only GSC impressions, clicks, and average position are used. Hashed client and content IDs join rows and keep clients together in validation; they are not model features or public outputs. I exclude the fixed 90-day query table because its recent window can overlap the June label. I also exclude GA4 values because their early availability differs by client and unavailable values may be zero-filled. The paper publishes only aggregate values and charts.

## Methodology

The May input features are log May impressions, log May clicks, May CTR, May average position with missing values filled for computation, and a separate missing-position indicator. The indicator keeps a filled zero from being treated as a real position. The observed decline label is 1 when June impressions are more than 20% below May impressions. It is a search outcome, not a refresh-success label.

The transparent baseline is the Week 5–7 rule. Pages above the training clients' median May impressions receive the main visibility score; low CTR and average position from 11 through 20 add priority. A small log-impression term breaks ties. The low-CTR threshold is the lower quartile of the training clients. The comparison model is scaled logistic regression on the same five May fields. Neither method receives June values or client IDs as inputs.

I used one GroupShuffleSplit with 25% of clients held out and seed 42. The resulting test set has 11 clients and 10,763 pages; no client appears in both training and test sets. The rule and model are compared on exactly those test pages. A client split tests a different set of clients at the same May cutoff. It does not test a later month.

At the end of June, a separate descriptive check counts page declines for each client. A broad-drop flag requires at least 30 complete May–June pages, more than 60% with a drop greater than 20%, and a drop greater than 20% in their combined impressions. The broad flag is not an input to the May prediction. It routes an editor to investigate the client's shared pattern before deciding on page edits.

## Results and interpretation

The held-out June decline base rate was 65.5%. Among the top 20 test pages, the rule selected 95.0% with an observed June decline and the model selected 80.0%. That means one rule selection and four model selections did not meet the decline label; these are errors against a search proxy, not proof that those pages need no attention. Among the top 50, the rule reached 92.0% and the model 82.0%. Model accuracy was 63.7%, below the 65.5% majority-class accuracy. On this split, I would keep the simple rule as the review queue. The high base rate matters: even a strong top-20 number is less impressive when nearly two thirds of held-out pages declined.

In the complete-window May–June subset, 14 of 32 clients with at least 30 matched pages meet the broad-drop rule. Another 18 have enough pages but do not meet it, and 10 observed clients have too few matched pages. In the June queue, 66,716 pages route to broader investigation first, 27,938 to page evidence review, and 2,024 to coverage checks. Eleven of the rule's top 20 June queue pages belong to flagged clients. The queue still contains every page; the flag changes what an editor checks first.

The count moves when a threshold changes. With the other main thresholds held fixed, requiring a page loss above 10%, 20%, or 30% flags 14, 14, or 11 clients. Requiring more than 50%, 60%, or 70% declining pages flags 14, 14, or 11. Requiring combined loss above 10%, 20%, or 30% flags 15, 14, or 10. Requiring at least 20, 30, or 40 matched pages flags 15, 14, or 14. This is a sensitivity check, not proof that the chosen cutoffs are optimal.

## Limitations

The model comparison uses one split and one May cutoff. The rule and model have not been tested on July outcomes or another period. The decline label is based only on impressions; changes in demand, indexing, search appearance, measurement, or other factors may explain it. Search Console reporting has privacy filtering and row limits. The 60-day coverage rule and 100 May-impression floor exclude newer and smaller pages, and clients contribute very different numbers of pages. The broad rule is a review heuristic and has not been validated as a prediction of future declines. No page refresh was tested, so these results do not measure the effect of a refresh or tell an editor which edit will work.

## Ranked recommendations

1. **Check coverage.** If a client has fewer than 30 matched complete-window pages, gather more history before labeling its pattern broad or local.
2. **Investigate flagged clients first.** Check Search Console coverage and indexing, compare clicks and positions, review seasonal demand, and look for shared site or template changes. Record what the evidence supports before editing many pages.
3. **Review page evidence.** For clients without a broad flag, use the transparent queue to select pages for a human check of content accuracy, search intent, titles, snippets, relevance, and internal links. A refresh, CTR edit, or no change remains an editor's decision.
4. **Record and monitor.** Keep the route, decision, and later outcome so the rule can be tested on another cutoff. A separate intervention design would be needed to estimate the impact of refreshes.

## Reproducibility and data availability

The self-contained capstone notebook is under work/notebooks/capstone.ipynb. The earlier checks are in work/notebooks/w03_feature_leakage_check.ipynb, w05_model.ipynb, w06_validation_audit.ipynb, and w07_action_playbook.ipynb. The three aggregate SVG figures are under work/figures. The notebook uses seed 42 for the grouped split and logistic regression.

From a fresh clone, create a Python environment, install requirements.txt and ipykernel, then open the capstone notebook in VS Code and select Run All. Accept the gate on the FlyRank Hugging Face dataset page and supply a read token as HF_TOKEN or through the notebook's hidden prompt. The first run streams the May and June partitions in DuckDB with a 1 GB memory limit and two threads and writes ignored local caches under work/outputs. Later runs use the caches. The gated page-level data are not redistributed in this repository.

## Acknowledgments and data credit

[Built on the FlyRank ML Internship dataset](https://flyrank.ai). The gated [FlyRank warehouse release](https://huggingface.co/datasets/FlyRank/internship-warehouse/blob/main/README.md) provides the data. [Google Search Central's traffic-drop guide](https://developers.google.com/search/docs/monitor-debug/debugging-search-traffic-drops) informed the scope question; it does not endorse this rule. [Google's Search Console data note](https://developers.google.com/search/blog/2022/10/performance-data-deep-dive) informs the measurement limitation.
