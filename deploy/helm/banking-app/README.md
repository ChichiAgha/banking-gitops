# banking-app Helm Chart

This chart deploys:
- banking-frontend
- user-service
- transaction-service
- activity-service

Runtime configuration model:
- Non-secrets from ConfigMap
- Secrets from AWS Secrets Manager through External Secrets

## Install (dev)

```bash
helm upgrade --install banking-app ./deploy/helm/banking-app \
  -f ./deploy/helm/banking-app/values.yaml \
  -f ./deploy/helm/banking-app/values-dev.yaml
```

## Install (prod)

```bash
helm upgrade --install banking-app ./deploy/helm/banking-app \
  -f ./deploy/helm/banking-app/values.yaml \
  -f ./deploy/helm/banking-app/values-prod.yaml
```

## Notes

- API endpoints are exposed through ALB ingress paths.
- Frontend is expected to call relative paths:
  - /api/usersvc
  - /api/txsvc
  - /api/activitysvc
