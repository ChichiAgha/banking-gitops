# Banking application Helm chart

This chart deploys the frontend, user, transaction, and activity services. PostgreSQL, Redis, and Kafka endpoints are runtime configuration; use managed services in production.

## Portability model

- Build one image per service and promote the same immutable Git SHA tag between environments.
- Override `images.*` and `images.tag` from the GitOps environment values; never rebuild for an environment.
- The browser uses relative URLs only.
- Frontend Nginx receives backend Kubernetes service names and ports at container startup and strips API prefixes before proxying.
- Non-secret settings come from `banking-config`; credentials come from External Secrets.
- Containers run as non-root with Linux capabilities dropped.

## Prerequisites

- Kubernetes 1.25+
- Helm 3
- An ingress controller matching `ingress.className`
- External Secrets Operator and the configured `ClusterSecretStore`
- For observability resources: Prometheus Operator CRDs from `kube-prometheus-stack`

The external secret must provide `DATABASE_URL`, `SPRING_DATASOURCE_USERNAME`, and `SPRING_DATASOURCE_PASSWORD`.

## Validate and deploy

```bash
helm lint deploy/helm/banking-app -f deploy/helm/banking-app/values-dev.yaml

helm upgrade --install banking-app deploy/helm/banking-app \
  --namespace banking --create-namespace \
  -f deploy/helm/banking-app/values.yaml \
  -f deploy/helm/banking-app/values-dev.yaml \
  --set images.userService=REGISTRY/bank-user-service \
  --set images.transactionService=REGISTRY/bank-transaction-service \
  --set images.activityService=REGISTRY/bank-activity-service \
  --set images.frontend=REGISTRY/bank-frontend \
  --set images.tag=GIT_SHA
```

In production, use a schema migration job/tool and set `config.springJpaHibernateDdlAuto` to `validate`; do not let application replicas mutate database schema.

See `docs/microservices-flow.md` for the request/event flow and `observability/README.md` for Datadog and Prometheus/Grafana/Alertmanager installation.
