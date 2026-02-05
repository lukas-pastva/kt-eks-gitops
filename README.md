# KT-EKS-GITOPS

- This repo is for ArgoCD app-of-apps for EKS example.

## TODO - Required AWS Configuration

IAM roles and policies are created via Terragrunt (`kt-eks-infra`). Run these commands to get the role ARNs and set them in the corresponding values files under `cluster-kt-ops-eks-1/values/`:

| Role | Command | File | Field |
|------|---------|------|-------|
| aws-load-balancer-controller | `aws iam get-role --role-name kt-ops-eks-1-aws-load-balancer-controller --query Role.Arn --output text` | `aws-load-balancer-controller.yaml` | `serviceAccount.annotations.eks.amazonaws.com/role-arn` |
| external-dns | `aws iam get-role --role-name kt-ops-eks-1-external-dns --query Role.Arn --output text` | `external-dns.yaml` | `serviceAccount.annotations.eks.amazonaws.com/role-arn` |
| efs-csi-driver | `aws iam get-role --role-name kt-ops-eks-1-efs-csi-driver --query Role.Arn --output text` | `aws-efs-csi-driver.yaml` | `controller.serviceAccount.annotations.eks.amazonaws.com/role-arn` |

### EFS Filesystem (required for WordPress autoscaling)

WordPress uses EFS (ReadWriteMany) so multiple pods can share files. Set the EFS filesystem ID in `aws-efs-csi-driver.yaml`:

```bash
aws efs describe-file-systems --query "FileSystems[?Name=='kt-ops-eks-1'].FileSystemId" --output text
```

Set the output in `aws-efs-csi-driver.yaml` → `storageClasses[0].parameters.fileSystemId`.

If no EFS filesystem exists yet, create one via Terragrunt or manually:

```bash
aws efs create-file-system --performance-mode generalPurpose --throughput-mode bursting --tags Key=Name,Value=kt-ops-eks-1 --region eu-central-1 --query FileSystemId --output text
```

## Install ArgoCD (without Terragrunt)

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd --version 9.4.0 --namespace argocd --create-namespace \
  -f kt-eks-gitops/cluster-kt-ops-eks-1/values/argocd.yaml
```

## Post-Install - Add Repository to ArgoCD

After ArgoCD is installed via Terragrunt, you need to add this GitOps repository in ArgoCD so it can manage the app-of-apps. Go to ArgoCD UI → Settings → Repositories → Connect Repo and add:

- **URL:** `git@github.com:lukas-pastva/kt-eks-gitops.git`
- **Type:** Git
- **Connection:** Via SSH (add a deploy key) or HTTPS (with a personal access token)