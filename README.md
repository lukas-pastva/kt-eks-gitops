# KT-EKS-GITOPS

- This repo is for ArgoCD app-of-apps for EKS example.

## TODO - Required AWS Configuration

IAM roles and policies are created via Terragrunt (`kt-eks-infra`). Run these commands to get the role ARNs and set them in the corresponding values files under `cluster-kt-ops-eks-1/values/`:

| Role | Command | File | Field |
|------|---------|------|-------|
| aws-load-balancer-controller | `aws iam get-role --role-name kt-ops-eks-1-aws-load-balancer-controller --query Role.Arn --output text` | `aws-load-balancer-controller.yaml` | `serviceAccount.annotations.eks.amazonaws.com/role-arn` |
| cluster-autoscaler | `aws iam get-role --role-name kt-ops-eks-1-cluster-autoscaler --query Role.Arn --output text` | `cluster-autoscaler.yaml` | `rbac.serviceAccount.annotations.eks.amazonaws.com/role-arn` |
| external-dns | `aws iam get-role --role-name kt-ops-eks-1-external-dns --query Role.Arn --output text` | `external-dns.yaml` | `serviceAccount.annotations.eks.amazonaws.com/role-arn` |

## Post-Install - Add Repository to ArgoCD

After ArgoCD is installed via Terragrunt, you need to add this GitOps repository in ArgoCD so it can manage the app-of-apps. Go to ArgoCD UI → Settings → Repositories → Connect Repo and add:

- **URL:** `git@github.com:lukas-pastva/kt-eks-gitops.git`
- **Type:** Git
- **Connection:** Via SSH (add a deploy key) or HTTPS (with a personal access token)