# Deploy to Kubernetes Cluster using Helm Charts

This projects uses Helm templates to deploy Docker images to Kubernetes cluster. Jenkins stages summary:
- `mvn build`: builds .war file
- `Docker Build`: Build tomcat docker image with war file from previous stage. Creates tags for both dev and prod, and pushes to docker repo.
- `Dev Deploy`: run helm install/upgrade using dev values file, which referes to dev image tag.
- `Prod Deploy`: run helm install/upgrade using prod values file, which referes to prod image tag.

## Known Issues/Improvements Required:
- I was unable to get ingress working with local kubernetes cluster using Kind. So Health Check stage in Jenkins pipeline is not added.
- Helm charts can be published to helm repository.