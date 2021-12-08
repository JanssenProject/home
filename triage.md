# Janssen Triage Process and labels

Triage process is used to quickly screen and categorise new issues and PRs. Aim is to determine characteristics of new PRs/issues and take quick actions if possible. 

Triage process is a contineous process. It does not take place during a call or a meeting only. Community holds triage call at a regular cadence but community members add their inputs to Issue/PR as it gets created without waiting for triage call. Triage calls are mainly utilised to discuss any point where concensus is not reached and to pick up anything which is not yet triaged. It is encouraged to complete triage outside of triage call if possible to improve velocity. 

### Stages in triage process:

|Stage|Name|Description|
| --- | --- | --- |
|1|Needs triage|When a new issue or PR is created, it is automatically labeled as `needs-triage`|
|2|Ready for triage|For all issues/PRs with `needs-triage` label, Maintainers/committers will assign a suitable active community <br/>member to start evaluating issue/PR as owner. Owner and community will discuss merit and characteristics of issue/PR and assign approapriate [labels](#labels). When owner believes that sufficient details have been added and there is concensus, the owner would remove <br/>`needs-triage` label and apply label `ready-for-triage`.|
|3|Triaged|All issues/PRs with label `ready-for-triage` will be reviewed by few core members of the community who have permission to add `triaged` label. <br/>This label indicates the triage is over for issue/PR. These members will <br/>apply `triaged` label when they see there is concensus and enough details have <br/>been added in form of labels. They mark these issues with `triaged` label  and <br/>remove `ready-for-triage` label. These issues/PRs will directly move to product <br/>backlog without needing to be discussed in triage call|

We expect most of the isseus and PRs will be able to follow above path and quickly move out of triage process without waiting for triage call. Issues/PRs which still doesn't have `triaged` label, or has `needs-discussion` label, will be discussed during triage call.

```
TODO: create flowchart or diagram for easy understanding of steps
```

## Labels

Github labels help us annotate issues/PRs with additional data. Janssen community uses labels, as detailed below, to communicate information and help decision making about issues/PRs.

### Communication labels

These labels communicate status of current triage process for an issue/PR. Also, there are labels like `good-first-issue` or `help-needed`. These labels used to indicate where community contribution is required. Most of communication labels would be replaced by other labels as triage process progresses and issue enters active development after completion of triage. For example, `help-needed` would be removed once issue is under active development and a community members takes ownership of the issue. Below is the list of such labels:

|Label|Indicates That|
| --- | --- |
|`needs-triage`|issue/PR needs triaging|
|`ready-for-triage`|that sufficient details has been added to the issue/PR in form of labels and is ready for triage review|
|`triaged`|Issue/PR is fully triaged|
|`needs-information`|Indicates that creator needs to add more information to issue/PR in order to be meaningfully triaged||
|`needs-discussion`|Indicates that issue needs discussion during triage meeting||
|`good-first-issue`|Indicates to the community that this issue suitable for first time contributor||
|`help-needed`|Indicates to the community that this issue has complexity which is suitable for contribution from any external contributor||

### Metadata labels

These labels enrich issue/PR with metadata that will help during triage process and active development. These labels may not be removed from issue/PR, though value of labels may change as development progresses. For example, `effort` label may change from `effort/s` to `effort/xl` as we understand issue more. Below is the list of such labels:

|Label|Indicates That|Details|
| --- | --- | --- |
|`comp:<module>`|Major Janssen components needing change in order to fix this issue/PR|e.g `comp:jans-auth-server`,`comp:jans-fido2`,|
|`area:<concern>`|Cross-cutting concerns involved in fixing this issue/PR|e.g `area:documentation`, `area:release-notes`, `area:CI`|
|`kind:bug`|Issue/PR is a bug in existing functionality||
|`kind:enhancement`|Issue/PR is an enhancement to an existing functionality||
|`kind:feature`|Issue/PR is new feature request||
|`kind:support`|Issue/PR is a question that can be addressed via pointers to documentation or user education||
|`effort:1`|Relative effort required for completion||
|`effort:2`|Relative effort required for completion||
|`effort:3`|Relative effort required for completion||
|`effort:5`|Relative effort required for completion||
|`effort:8`|Relative effort required for completion||
|`effort:13`|Relative effort required for completion||
|`effort:21`|Relative effort required for completion||
|`priority:P0`|An issue that causes a full outage, breakage, or major function unavailability for everyone, without any known workaround. The issue must be fixed immediately, taking precedence over all other work. Should receive updates at least once per day.||
|`priority:P1`|An issue that significantly impacts a large percentage of users; if there is a workaround it is partial or overly painful. The issue should be resolved before the next release.||
|`priority:P2`|The issue is important to a large percentage of users, with a workaround. Issues that are significantly ugly or painful (especially first-use or install-time issues). Issues with workarounds that would otherwise be P0 or P1.||
|`priority:P3`|An issue that is relevant to core functions, but does not impede progress. Important, but not urgent.||
|`priority:P4`|A relatively minor issue that is not relevant to core functions, or relates only to the attractiveness or pleasantness of use of the system. Good to have but not necessary changes/fixes.||
|`priority:P5`|The team acknowledges the request but (due to any number of reasons) does not plan to work on or accept contributions for this request. The issue remains open for discussion.||


### Status labels

These labels will help in planing and tracking active development activities for a issue/PR.

|Label|Indicates That|Details|
| --- | --- | --- |
|TBD|TBD|TBD|
