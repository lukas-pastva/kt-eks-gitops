# KT-EKS-GITOPS

- This repo is for ArgoCD app-of-apps for EKS example.

## TODO - Required AWS Configuration

IAM roles and policies are created via Terragrunt. Use these commands to retrieve the values needed in `cluster-kt-ops-eks-1/values/`:

### cluster-autoscaler.yaml

```bash
# rbac.serviceAccount.annotations.eks.amazonaws.com/role-arn
aws iam get-role --role-name kt-ops-eks-1-cluster-autoscaler --query Role.Arn --output text
```

### aws-load-balancer-controller.yaml

```bash
# serviceAccount.annotations.eks.amazonaws.com/role-arn
aws iam get-role --role-name kt-ops-eks-1-aws-load-balancer-controller --query Role.Arn --output text
```

### external-dns.yaml

```bash
# serviceAccount.annotations.eks.amazonaws.com/role-arn
aws iam get-role --role-name kt-ops-eks-1-external-dns --query Role.Arn --output text
```