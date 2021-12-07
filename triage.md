# Janssen Triage Process and labels

Triage process is used to quickly screen and categorise new issues and PRs. Aim is to determine characteristics of new PRs/issues and take quick actions if possible. 

Community holds triage call at a regular cadence. But triage process itself does not take place just during the call, rather it is encouraged to complete triage outside of triage call to improve velocity. Community members add their inputs to Issue/PR as it gets created without waiting for triage call. Triage calls are mainly utilised to discuss any point where concensus is not reached and to pick up anything which is not yet triaged.

High-level steps in triage process:

1) Needs triage : New issue or PR is created. It automatically gets a `needs-triage` label
2) Ready for triage: Maintainers, committers assign issue/PR to suitable active community member to start evaluating issue/PR and as a result assign labels for `kind/`, `size/`, `needs-discussion`, `duplicate` etc [labels](#labels). At this stage, `needs-triage` label is removed and `ready-for-triage` label is applied.
3) Triaged:Few core members of the community have permission to add `triaged` label. This label indicates the triage is over for issue/PR. These members will apply `triaged` label when they see there is concensus and enough details have been added in form of labels. They mark these issues with `triaged` label  and remove `ready-for-triage` label. These issues/PRs will directly move to product backlog without needing to be discussed in triage call.

We expect most of the isseus and PRs will be able to follow above path and quickly move out of triage process. Remaining issues with `needs-triage` and `ready-for-triage` labels will be discussed during triage call.

## Labels

|Label|Description|Details|
| --- | --- | --- |
|`needs-triage`|Identifies a new issue|*Applied when*: New issue/PR is raised.<br/>*Applied by*: Automatically applied by workflow<br/>*Removed when*: Owner/community has finished adding sufficient labeling information<br/>*Removed by*: Owner of issue/PR|



