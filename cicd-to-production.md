# CI/CD with Jenkins (Declarative and Templated Pipelines)

## Overview
This case study covers my work designing and maintaining **production-grade CI/CD pipelines using Jenkins**, supporting multiple technology stacks and deployment targets. The focus was on creating **reusable, template-driven pipelines** that could handle different application types while enforcing consistent quality gates and controlled production releases.

The pipelines were used for **production deployments**, with **manual approvals handled via XLR**.

---

## Problem
The organization supported applications built using **multiple technology stacks** (Java/Maven, Node.js, and .NET), each with different build tools, dependency management approaches, and quality requirements.

Key challenges included:
- Inconsistent CI stages across teams
- Duplication of Jenkins pipeline logic
- Lack of standardized quality gates before production releases
- Manual coordination between CI, release approvals, and deployment execution

---

## Constraints
- Production deployments required **manual approvals in XLR**
- Multiple build ecosystems had to be supported:
  - Maven (`pom.xml`)
  - Node.js (`npm`)
  - .NET (`msbuild`, NuGet)
- CI pipelines needed to integrate with:
  - SonarQube for code quality analysis
  - JFrog Artifactory for binary storage
- Target environments included a **mix of VMs and Kubernetes**

---

## Solution
I implemented a combination of **Declarative Jenkins Pipelines** and **templated Groovy-based pipelines** to standardize CI while keeping stack-specific behavior configurable.

### Pipeline Structure & CI Stages
Each pipeline followed a consistent, well-defined set of stages:

1. **Build & Dependency Resolution**
   - Maven builds driven via `pom.xml`
   - Node.js dependency resolution via `npm`
   - .NET builds using `msbuild`
   - For .Net Projects dependency management was done via **NuGet**, including creation of NuGet packages where required

2. **Unit Testing**
   - Executed stack-appropriate unit tests
   - Configured **fail-fast behavior** to stop the pipeline immediately if tests failed

3. **Code Quality Analysis**
   - Integrated **SonarQube** analysis into the pipeline
   - Enforced quality gates
   - Pipeline execution failed automatically if code quality thresholds were not met

4. **Artifact Packaging & Binary Repository Deployment**
   - Built artifacts packaged consistently across stacks
   - Artifacts published to **JFrog Artifactory**
   - Jenkins acted as the system of record for build outputs

5. **Release & Deployment Integration**
   - Jenkins pipelines triggered **XLR** for release orchestration
   - Manual approval gates enforced in XLR before production deployments
   - Deployment execution handled via **Ansible Tower**

6. **Notifications**
   - Notifications sent on:
     - pipeline failures
     - quality gate violations
     - successful pipeline completion
   - Enabled faster feedback and quicker troubleshooting

---

## Jenkins Pipeline Design
- Implemented **template-based Groovy pipelines** to avoid duplication
- Centralized common CI logic:
  - checkout
  - build
  - test
  - quality analysis
  - artifact publishing
- Allowed application teams to onboard quickly by reusing pipeline templates with minimal configuration

---

## Security & Reliability Considerations
- **Fail-fast conditions** for:
  - unit test failures
  - SonarQube quality gate violations
- Clear separation of responsibilities:
  - Jenkins for CI and artifact generation
  - XLR for release control and approvals
  - Ansible Tower for deployment execution
- Reduced risk of accidental production changes through enforced approval flows

---

## Outcome
- Standardized CI pipelines across multiple technology stacks
- Improved build consistency and code quality enforcement
- Reduced manual intervention during CI while retaining controlled production releases
- Faster onboarding of new applications through reusable pipeline templates
- Increased confidence in production deployments due to early failure detection

---

## What I Would Improve Next
- Move more shared logic into Jenkins shared libraries to reduce Groovy complexity
- Improve pipeline observability with clearer metrics and reporting
- Introduce additional automated validation earlier in the CI lifecycle
- Gradually reduce manual approval scope where risk allows
