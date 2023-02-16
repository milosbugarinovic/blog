# File conventions

## File/folder names

We use [`kebab-case`](/draft/convention/legend-naming-convention.md)

Name should be singular. Folders are design to store files, and we are never going to have a folder that needs to store only one
file :man_shrugging:

```
/src/business/service/some-service.ts
/src/util/login.ts
/src/some-folder/some-file.ts
```

## File and Class name

Class name if flowing the file name. If we have multiple classes with similar functionality we can move and group them in a folder
and use the folder name in class name making the file name shorter.

| Class name                                   | file with path                                                               |
|----------------------------------------------|------------------------------------------------------------------------------|
| ReimbursementClaimService                    | `src/business/service/reimbursement/claim/service.ts`                        |
| ReimbursementClaimItemService                | `src/business/service/reimbursement/claim/item/service.ts`                   |
| ReimbursementClaimStatusRuleStrategy         | `src/business/service/reimbursement/claim/status-rule/strategy/index.ts`     |
| ReimbursementClaimStatusRuleStrategyAllowAll | `src/business/service/reimbursement/claim/status-rule/strategy/allow-all.ts` |
| ReimbursementClaimStatusRuleService          | `src/business/service/reimbursement/claim/status-rule/service.ts`            |

