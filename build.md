# Build

## Notes

- Each operation is performed in an immutable container with provenance and signed attestation of all generated artifacts
- Each branch is a step that should be performed in parallel
- The build should fail if any attestation fails (leveraging in-toto & SPIRE)

\* indicates that this could happen at a different frequency based upon compute/cost/risk tradeoffs; the minimum frequency is that these steps must be completed before the PR may be merged

## Pre-commit

```mermaid
graph LR
  Pre-commit --> Secret(Secret scan)
```

## Per-commit

```mermaid
graph LR
  Per-commit --> Linters
  Linters --> Language(Language-specific)
  Linters --> IaC("Infrastructure as Code (IaC)")
  Linters --> OpenAPI
  Per-commit --> Compile
  Compile --> OpenAPISpec[(OpenAPI specs)]
  OpenAPISpec --> OpenAPI
  Compile --> CodeCoverage(Code Coverage)
  CodeCoverage --> UnitTest(Unit tests)
  CodeCoverage --> IntegrationTest(Integration tests)
  UnitTest --> Coverage("Coverage aggregation (e.g. Sonar)")
  IntegrationTest --> Coverage
  CodeCoverage --> Container("Container / Buildpack")
  Linters --> Container
  Container --> SCA("SCA Scan*") --> Manifest[(Manifest / Notices)]
  Container --> ContainerScan("Container scan")
  ContainerScan --> SignedContainer[(Signed Identity)]
  ContainerScan --> Image[(Published image)]
  Per-commit --> SAST("Static security scans (SAST)*")
```

## Per-pull request (or per update to PR)

```mermaid
graph LR
  PR(Per-PR) --> Deploy("Deploy to test environment*")
  Deploy --> SystemTests(System tests) --> Chaos(Reliability / chaos testing)
  Deploy --> Dynamic("Dynamic security scans (DAST)")
  Deploy --> API(API fuzzing scan)
  Deploy --> InfrastructureVuln(Infrastructure vulnerability scan)
  Deploy --> InfrastructureConfig(Cloud misconfiguration scan)
  Deploy --> Performance(Performance tests)
  PR --> Peer(Peer review)
```

## Per-merge

\*\* Publication/promotion to production upon stakeholder approval depends upon risk tolerance

```mermaid
graph LR
  Merge(Per-merge) --> Approval("Stakeholder approval")
  Approval --> SagingRepo(Publish artefacts to staging repository) --> Deploy(Deploy to staging/beta)
  Approval -.-> Promote(Publish arefacts to production repository**)
```

## Per-deployment

TODO

## Pipeline/build provisioning

TODO
