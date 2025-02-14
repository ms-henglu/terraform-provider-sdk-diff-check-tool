# Cross Version Import Tool

## Introduction
This solution is used to detect SDK behavioral change when upgrade SDK for terraform providers. It takes advantage of existing acceptance tests. By deploying resources with released provider and using import step with developing provider to check whether there's a behavioral change.

## How to use in ADO pipeline?
1. Push your codes to your fork. 
2. Run pipeline from [this page](https://dev.azure.com/azclitools/internal/_build?definitionId=49).
   1. Input the target fork, for example: ms-henglu.
   2. Input the target branch, for example, branch-update-network-sdk
   2. Input the target resource provider, for example, network.
   3. Input tested Acc test's prefix.
   4. Edit other environment variable if needed.
      1. Input standard provider version if needed. It will use latest released provider by default.

## How to use in local?
1. Cherry-pick the ahead commits in [this branch](https://github.com/ms-henglu/terraform-provider-azurerm/tree/feature-breaking-change-detection).
```shell
git remote add mshenglu https://github.com/ms-henglu/terraform-provider-azurerm.git
git fetch mshenglu
git cherry-pick 9075dd4afc721d18fce7bd105c1514b03cb5ca5e
```
2. Run acceptance test. If `ImportStateVerify attributes not equivalent`, it means there's a behavioral change during SDK upgrade.

## How does it work?
In import step of acceptance test, it will call import command to get state from backend and verify that all the states match with deployed states. In this solution, it uses import command but with new SDK to check whether there's a change comparing to deployed states.
