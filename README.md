# vul-plugin-referrer

Vul plugin for the OCI referrer

## Install

```
$ vul plugin install github.com/khulnasoft/vul-plugin-referrer
```

## Usage

### Putting the SBOM into the OCI registry

Put the SBOM into the OCI registry as a referrer.
You need the write permission for the target repository.
```
# CycloneDX
$ vul image -q -f cyclonedx YOUR_IMAGE | vul referrer put

# SPDX
$ vul image -q -f spdx-json YOUR_IMAGE | vul referrer put
```

You can also upload by specifying a file.
```
$ vul image -q -f cyclonedx YOUR_IMAGE > sbom.cdx.json
$ vul referrer put -f sbom.cdx.json
```

### Putting the SARIF into the OCI registry
```
# NOTICE: Since Vul v0.38.3 doesn’t write subject identification in SARIF, you need to explicitly state the subject.
$ vul image -q -f sarif YOUR_IMAGE | vul referrer put --subject YOUR_IMAGE
```

### Putting the Vulnerability info into the OCI registry
```
$ vul image -q -f cosign-vuln YOUR_IMAGE | vul referrer put
```

### Listing referrers
```
$ vul referrer list localhost:5002/demo:app --type cyclonedx
Subject:                                     localhost:5002/demo:app
Referrers:                                    
  Digest:                                    sha256:5b0306dbbe8252a9efaa7a5177d5ae313441aa85c3d994b2183726303fe52101
  Reference:                                 localhost:5002/demo@sha256:5b0306dbbe8252a9efaa7a5177d5ae313441aa85c3d994b2183726303fe52101
  MediaType:                                 application/vnd.oci.image.manifest.v1+json
  ArtifactType:                              application/vnd.cyclonedx+json
  Annotations:                               
    created-by:                              vul
    org.opencontainers.artifact.created:     2023-04-11T19:21:29+09:00
    org.opencontainers.artifact.description: CycloneDX JSON SBOM


$ vul referrer list localhost:5002/demo:app --format table
DIGEST  TYPE      ANNOTATIONS       DESCRIPTION         CREATED
5b0306d cyclonedx created-by=vul  CycloneDX JSON SBOM 22 hours ago
771989f spdx-json created-by=vul  SPDX JSON SBOM      22 hours ago
83542d1 sarif     created-by=vul  SARIF               18 hours ago
8b9f058 sarif     created-by=vul  SARIF               15 hours ago
```

### Getting the artifact

```
$ vul referrer get localhost:5002/demo:app --digest 5b0306d | head
{
  "bomFormat": "CycloneDX",
  "specVersion": "1.4",
  "serialNumber": "urn:uuid:2512b7b2-ed10-4526-9b64-dc32869eb401",
  "version": 1,
  "metadata": {
    "timestamp": "2023-04-11T10:21:29+00:00",
    "tools": [
      {
        "vendor": "khulnasoft-lab",

```

### Listing referrers recursively

```
$ vul referrer tree localhost:500/demo:app
Subject: localhost:5002/demo:app

a5cb013
├── 5b0306d: application/vnd.cyclonedx+json
│   └── c248e29: application/vnd.cncf.notary.signature
├── 771989f: application/spdx+json
├── 83542d1: application/sarif+json
└── 8b9f058: application/sarif+json
```
