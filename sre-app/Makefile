.PHONY: setup install cleanup

# Variables
SECRET_NAME = sre-app-secrets
SECRET_KEY_BASE ="GWks+00R99rp4iCigXEDAP7WRQXFiTImOP0vIUmFyaNj1DGDXsMvEfSXtb7ITMKC"
NAMESPACE = stord

setup:
	@echo "Setting up Kubernetes secrets..."
	@kubectl create secret generic $(SECRET_NAME) \
		--from-literal=SECRET_KEY_BASE=$(SECRET_KEY_BASE) \
		--namespace=$(NAMESPACE) \
		--dry-run=client -o yaml | kubectl apply -f -
	@echo "Secrets setup complete."

install: setup
	@echo "Installing sre-db..."
	@helm install sre-db oci://registry-1.docker.io/bitnamicharts/postgresql \
		--set auth.username=postgres \
		--set auth.password=password \
		--set auth.database=sre-technical-challenge \
		--namespace=$(NAMESPACE)
	@echo "Installing sre-app..."
	@helm install sre-app . --namespace=$(NAMESPACE)
	@echo "Installation complete."

cleanup:
	@echo "Cleaning up deployments and secrets..."
	@helm uninstall sre-app --namespace=$(NAMESPACE) || true
	@helm uninstall sre-db --namespace=$(NAMESPACE) || true
	@kubectl delete job -l app.kubernetes.io/instance=sre-app --namespace=$(NAMESPACE) || true
	@kubectl delete secret $(SECRET_NAME) --namespace=$(NAMESPACE) || true
	@echo "Cleanup complete."
