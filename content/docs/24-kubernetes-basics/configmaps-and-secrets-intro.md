---
title: "ConfigMaps and Secrets Intro"
description: "Separating configuration and sensitive values from application images and code — ConfigMaps for plain configuration, Secrets for sensitive data, and why Secrets alone aren't real encryption."
module: "24-kubernetes-basics"
moduleTitle: "Kubernetes Basics"
stage: "professional"
difficulty: "expert"
foundational: false
type: "concept"
prerequisites: ["24-kubernetes-basics/namespaces"]
relatedTopics: ["namespaces", "which-linux-skills-k8s-depends-on"]
relatedCommands: ["kubectl"]
careerRelevance: ["devops", "cloud", "platform", "cybersecurity"]
relatedLabs: []
relatedInterviewQuestions: ["intermediate#k8s-secret-not-encrypted"]
relatedCheatsheet: ""
furtherReading: [{"label": "Kubernetes Secrets Documentation", "url": "https://kubernetes.io/docs/concepts/configuration/secret/"}]
nextTopic: "24-kubernetes-basics/which-linux-skills-k8s-depends-on"
prevTopic: "24-kubernetes-basics/namespaces"
estimatedReadingTime: 8
updatedAt: "2026-07-28"
keywords: ["kubernetes configmap explained", "kubernetes secret explained", "k8s secret base64 not encrypted", "configmap vs secret"]
canonicalUrl: "/docs/kubernetes-basics/configmaps-and-secrets-intro"
---

# ConfigMaps and Secrets Intro

🔴 Expert · Relevant for: DevOps · Cloud · Platform · Cybersecurity

> **TL;DR:** ConfigMaps hold non-sensitive configuration; Secrets hold sensitive values — both
> keep configuration out of container images, exactly [Module 23's](../23-docker-basics/docker-best-practices.md)
> "never bake secrets into an image" principle applied at the Kubernetes level. Critically, a
> Secret is only base64-*encoded*, not encrypted, by default — a distinction that matters.

## What is it?

Two Kubernetes objects for injecting configuration into Pods without baking it into a container
image: ConfigMaps for ordinary, non-sensitive configuration, and Secrets for sensitive values —
extending [Module 23's Docker Best Practices](../23-docker-basics/docker-best-practices.md)
secrets-handling principle to the Kubernetes layer.

## Why does it exist?

Baking configuration directly into an image means rebuilding the image for every configuration
change, and baking secrets into an image (or a manifest committed to version control) means they
become permanently embedded in that image's or repository's history. ConfigMaps and Secrets
separate configuration and sensitive data from both the image and the application code, injected
at runtime instead.

## Where is it used?

Any application needing environment-specific configuration (a database connection string that
differs between `staging` and `production` [Namespaces](namespaces.md)) or sensitive values (API
keys, database passwords) that must never be committed directly into a Dockerfile, image layer, or
plain manifest file.

## How it works

> 📊 Diagram: a Pod definition referencing a separate ConfigMap object (for a non-sensitive
> setting like a feature flag) and a separate Secret object (for a database password), both
> injected into the Pod as environment variables or mounted files at runtime — with the Secret
> object shown explicitly labeled "base64-encoded, not encrypted" as a callout distinct from the
> ConfigMap.

**ConfigMap** — non-sensitive configuration:

```bash
kubectl create configmap app-config --from-literal=LOG_LEVEL=debug
```

```yaml
# referenced in a Pod spec
env:
  - name: LOG_LEVEL
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: LOG_LEVEL
```

**Secret** — sensitive values:

```bash
kubectl create secret generic db-creds --from-literal=password=hunter2
```

```yaml
# referenced in a Pod spec
env:
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-creds
        key: password
```

**⚠️ The critical distinction — Secrets are encoded, not encrypted, by default:**

```bash
$ kubectl get secret db-creds -o jsonpath='{.data.password}'
aHVudGVyMg==

$ echo aHVudGVyMg== | base64 -d
hunter2
```

A Secret's value is stored base64-*encoded* in `etcd` by default — trivially reversible, not a
security control. Anyone with API access to read the Secret (or direct `etcd` access) can decode
it in seconds. Genuine encryption at rest requires additional cluster configuration
(encryption providers) that isn't automatic — a fact that surprises many newcomers who assume
"Secret" implies encryption by name alone.

## Real-world example

A team stores a database password in a Kubernetes Secret and considers the matter closed from a
security standpoint, assuming "Secret" means it's encrypted. A security review reveals that anyone
with `kubectl get secret -o yaml` access can decode the value in one command — the Secret object
only kept the password out of the image and out of plain-text manifests, but provided no actual
encryption at rest without additional, deliberately-configured cluster-level encryption. This
matches exactly the same lesson from [Module 23's Docker Best Practices](../23-docker-basics/docker-best-practices.md):
a mechanism that *looks* secure by name isn't automatically secure by default.

## Syntax

```
kubectl create configmap NAME --from-literal=KEY=VALUE
kubectl create secret generic NAME --from-literal=KEY=VALUE
```

## Commands

See [`kubectl`](../../commands/kubectl.md) — extended below with `create configmap`/`create
secret` coverage.

## Production example

```
$ kubectl create configmap app-config --from-literal=LOG_LEVEL=debug
configmap/app-config created

$ kubectl create secret generic db-creds --from-literal=password=hunter2
secret/db-creds created

$ kubectl get secret db-creds -o jsonpath='{.data.password}' | base64 -d
hunter2
```

Confirming directly that a Secret's value is trivially decodable by anyone with read access —
exactly the point this page emphasizes.

## Do / Don't

| Do | Don't |
|---|---|
| Use ConfigMaps for non-sensitive configuration, Secrets for sensitive values | Put sensitive values in a ConfigMap out of convenience |
| Configure cluster-level encryption at rest for genuine Secret confidentiality | Assume a Secret is encrypted just because of its name |
| Restrict RBAC access to Secrets tightly | Grant broad Secret read access as a matter of convenience |

## Common mistakes

- Assuming a Kubernetes Secret is encrypted by default, when it's only base64-encoded unless
  additional encryption-at-rest configuration is deliberately applied.
- Putting sensitive values in a ConfigMap because it's more familiar, missing the RBAC and
  handling distinctions Secrets are specifically designed for.
- Granting overly broad access to read Secrets, given how trivially their values can be decoded by
  anyone with that access.

## Best practices

- Use ConfigMaps and Secrets consistently by their intended distinction — never store sensitive
  values in a ConfigMap.
- Configure cluster-level encryption at rest for Secrets in any environment handling genuinely
  sensitive data — don't rely on base64 encoding as a security boundary.
- Restrict RBAC permissions for reading Secrets tightly, treating that access with the same care
  as direct database credential access.

## Exercises

1. Create a ConfigMap and a Secret with the same literal key-value pattern, then decode the
   Secret's stored value to confirm it's only base64-encoded.
2. Explain why "Secret" is a somewhat misleading name given its default behavior.
3. Describe what would be needed to make a cluster's Secrets genuinely encrypted at rest.

## Quiz

**Q: Is a Kubernetes Secret encrypted by default?**
<details><summary>Show answer</summary>
No — it's only base64-encoded by default, which is trivially reversible; genuine encryption at
rest requires additional, deliberately-configured cluster-level encryption providers.
</details>

**Q: What's the main practical difference between a ConfigMap and a Secret, given they behave similarly?**
<details><summary>Show answer</summary>
Secrets are intended for sensitive values and typically have more restrictive RBAC handling
conventions; ConfigMaps are for ordinary, non-sensitive configuration — the distinction is about
intended use and access control, not automatic encryption.
</details>

**Q: How does this page's Secret lesson connect to Module 23's Docker Best Practices?**
<details><summary>Show answer</summary>
Both teach that a mechanism can look secure by name or convention without actually providing
strong security by default — Module 23 warned against baking secrets into image layers; this page
warns that a Kubernetes "Secret" isn't automatically encrypted.
</details>

## Interview questions

- Is a Kubernetes Secret actually encrypted? Explain what protection it does and doesn't provide
  by default. →
  [Full answer in Intermediate Interview Questions](../../interview-questions/intermediate.md)

## Key Takeaways

- ConfigMaps hold non-sensitive configuration; Secrets hold sensitive values — both keep
  configuration out of images and code.
- A Secret is base64-*encoded*, not encrypted, by default — a critical, often-surprising
  distinction.
- Genuine encryption at rest for Secrets requires additional, deliberate cluster configuration.
- Restrict RBAC access to Secrets tightly, given how easily their values can be decoded.

## Related topics

- [Namespaces](namespaces.md)
- [Which Linux Skills K8s Actually Depends On](which-linux-skills-k8s-depends-on.md)
