# AWS Organization Terraform Module

## Usage

In this example with yaml conf we create 2 SCP policies: _dev_control_access_ and _deny_all_, use json policy from folder: `policies/scps/`

```terraform
module "aws_organization" {
  source  = "tminaorg/aws-organization/aws"
  version = "1.0.0"

  # variables are configured via yaml files inside "conf" folder
}
```

```yaml
# conf/aws_organization.yaml
---
# aws organization
feature_set: "ALL"
aws_service_access_principals: ["sso.amazonaws.com"]
enabled_policy_types: ["SERVICE_CONTROL_POLICY"]
```

```yaml
# conf/policies.yaml
---
policies:
  - name: "dev_control_access"
    template_file: "./policies/scps/dev_control_access.json"
  - name: "deny_all"
    template_file: "./policies/scps/deny_all.json"
```

```yaml
# conf/organizational_units.yaml
---
organizational_units:
  - name: "CoreOU"
    policies: []
    children:
      - name: "DevelopmentOU"
        policies: ["dev_control_access"]
        children: []
      - name: "StageOU"
        policies: []
        children: []
      - name: "ProductionOU"
        policies: []
        children: []
  - name: "SandboxOU"
    policies: ["deny_all"]
    children: []
```

```yaml
# conf/accounts.yaml
---
accounts:
  - name: "AccountInRootOU"
    email: "test+root@example.org"
    parent_id: ""
    policies: ["deny_all"]
  - name: "Development"
    email: "test+dev@example.org"
    parent_path: "CoreOU/DevelopmentOU"
  - name: "Stage"
    email: "test+stage@example.org"
    parent_path: "CoreOU/StageOU"
  - name: "Production"
    email: "test+shared@example.org"
    parent_path: "CoreOU/ProductionOU"
```

In this example with standard tf variables we create 2 SCP policies: _dev_control_access_ and _deny_all_, use json policy from folder: `policies/scps/`

```terraform
module "aws_organization" {
  source                        = "tminaorg/aws-organization/aws"
  version                       = "1.0.0"
  feature_set                   = "ALL"
  aws_service_access_principals = ["sso.amazonaws.com"]
  enabled_policy_types          = ["SERVICE_CONTROL_POLICY"]
  policies = [
    {
      name : "dev_control_access",
      template_file : "./policies/scps/dev_control_access.json",
    },
    {
      name : "deny_all",
      template_file : "./policies/scps/deny_all.json",
    }
  ]
  organizational_units = [
    {
      name : "CoreOU",
      policies : [],
      children : [
        {
          name : "DevelopmentOU",
          policies : ["dev_control_access"],
          children : []
        },
        {
          name : "StageOU",
          policies : [],
          children : []
        },
        {
          name : "ProductionOU",
          policies : [],
          children : []
        }
      ]
    },
    {
      name : "SandboxOU",
      policies : [],
      children : []
    }
  ]
  accounts = [
    {
      name : "AccountInRootOU",
      email : "test+root@example.org",
      parent_id : "",
      policies : ["deny_all"]
    },
    {
      name : "Development",
      email : "test+dev@example.org",
      parent_path : "CoreOU/DevelopmentOU"
    },
    {
      name : "Stage",
      email : "test+stage@example.org",
      parent_path : "CoreOU/StageOU",
    },
    {
      name : "Production",
      email : "test+shared@example.org",
      parent_path : "CoreOU/ProductionOU"
    }
  ]
}
```

## Examples

- [Complete AWS Organization using yaml config files](examples/complete-with-yaml)
- [Complete AWS Organization using Terraform variables](examples/complete-with-tf-vars)

<!-- BEGIN_TF_DOCS -->
<!-- END_TF_DOCS -->

## Authors

Module is maintained by [Nikola Kolovic](https://github.com/nikolakolovic) and [Aleksa Siriški](https://github.com/aleksasiriski) with help from [CyberLab Team](https://github.com/cyberlabrs).

## License

Apache 2 Licensed. See [LICENSE](LICENSE) for full details.
