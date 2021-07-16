# Cross Version Import Tool

## Introduction
This solution is used to detect SDK behavioral change when upgrade SDK for terraform providers. It takes advantage of existing acceptance tests. By deploying resources with released provider and using import step with developing provider to check whether there's a behavioral change.

## How to use in ADO pipeline?
1. Push your codes to Microsoft's fork. https://github.com/microsoft/terraform-provider-azurerm
2. Run pipeline from https://dev.azure.com/mseng/VSOSS/_build?definitionId=11477.
   1. Input test branch.
   2. Input tested package path.
   3. Input tested Acc test's prefix.
   4. Edit other environment variable if needed.
      1. Input standard provider version if needed. It will use latest released provider by default.
      2. It will automatically add missing import step by default. To disable this feature, set `TF_ACC_FIX_IMPORT=false`

## How to use in local?
Replace `terraform-provider-azurerm`'s `terraform-plugin-sdk` with `https://github.com/ms-henglu/terraform-plugin-sdk/tree/feature-support-state-import-from-different-version-v2.7.0`. Detailed steps are listed as the following.
1. clone `https://github.com/ms-henglu/terraform-plugin-sdk.git` to local directory  (ex:`C:\Users\henglu\go\src\github.com\ms-henglu\terraform-plugin-sdk`)
2. switch to `feature-support-state-import-from-different-version-v2.7.0` branch
3. open `terraform-provider-azurerm/go.mod`, and add the following line
`
replace github.com/hashicorp/terraform-plugin-sdk/v2 => C:\Users\henglu\go\src\github.com\ms-henglu\terraform-plugin-sdk
`
4. run `go mod vendor`
5. run acceptance test. If `ImportStateVerify attributes not equivalent`, it means there's a behavioral change during SDK upgrade.

## How does it work?
In import step of acceptance test, it will call import command to get state from backend and verify that all the states match with deployed states. In this solution, it uses import command but with new SDK to check whether there's a change comparing to deployed states.

## Advanced usage
There're some environment variables that can be configured.
1. `TF_ACC_CROSS_VERSION_IMPORT` : Whether enable cross version import feature. Accepted values are `true` and `false`, default to `true`.
2. `TF_ACC_PROVIDER`: The provider’s name used for test. Default to `azurerm`.
3. `TF_ACC_PROVIDER_VERSION`: The provider version used for comparison. Default to `` which will use latest released version.
4. `TF_ACC_PROVIDER_NAMESPACE`: The namespace of the provider used for test. Default to `hashicorp`.
