<div align="center">
	<p>
		<img alt="Thoughtworks Logo" src="https://raw.githubusercontent.com/twplatformlabs/static/master/psk_banner.png" width=800 />
	</p>
  <h2>gha-tools-actions</h2>
  <h4>for GitHub Actions pipelines</h4>
  <img alt="GitHub Actions Workflow Status" src="https://img.shields.io/github/actions/workflow/status/twplatformlabs/gha-tools-actions/.github%2Fworkflows%2Fdevelopment-build.yaml"> <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/license-MIT-blue.svg"></a>
</div>
<br />

Set of Actions providing capabilities to the gha-tools-workflows. Though those workflows are the primary users of these actions, they can be used individually as-needed.

Generally, these actions are designed to support the docker bake file build process by looping over bake file targets.  

For example, this bake file snippet defines two base image, each with both amd and arm architectures. The gha-tools-actions will parse the bake file, finding all four images and perform the associated build, scan, or other action against each of the images, or in this example, each of the two Dockerfiles.
```json
{
  "variable": {
    "CREATED": { "default": "" },
    "REVISION": { "default": "" },
    "TAG": { "default": "" }
  },

  "group": {
    "executors": {
      "targets": ["alpine", "ubuntu"]
    }
  },
  "target": {
    "_release": {
      "labels": {
        "org.opencontainers.image.created": "${CREATED}",
        "org.opencontainers.image.revision": "${REVISION}",
        "org.opencontainers.image.vendor": "ThoughtWorks, Inc.",
        "org.opencontainers.image.licenses": "MIT"
      },
      "args": {
        "BUILDKIT_CONTEXT_KEEP_GIT_DIR": "1",
        "BUILDX_EXPERIMENTAL": "1",
        "BUILDKIT_SBOM_SCAN_CONTEXT": "1"
      },
      "attest": [
        "type=provenance,mode=max,version=v1",
        "type=sbom"
      ]
    },
    "alpine": {
      "dockerfile": "Dockerfile.alpine",
      "tags": ["ghcr.io/twplatformlabs/runner:alpine-${TAG}"],
      "context": ".",
      "platforms": ["linux/amd64","linux/arm64"],
      "inherits": ["_release"],
      "labels": {
        "org.opencontainers.image.url": ""
      }
    },
    "ubuntu": {
      "dockerfile": "Dockerfile.ubuntu",
      "tags": ["ghcr.io/twplatformlabs/runner:ubuntu-${TAG}"],
      "context": ".",
      "platforms": ["linux/amd64","linux/arm64"],
      "inherits": ["_release"],
      "labels": {
        "org.opencontainers.image.url": ""
      }
    }
  }
}
```
### Actions
- **bats**: Launch each image defined by the bakefile and run the specified bats test against the running image.  
- **buildx**: Run `docker bake` referencing the defined bake file to build the defined images and architectures. Bake can include a general bill of materials and basic provenance in image manifest.  
- **confirm-registry**: Run a `docker login` using the defined registry, confirming the credentials and making registry access available.
- **hadolint**: Run hadolint scan against bake defined Dockerfiles.  
- **install**: Install action tool dependencies when not using pre-configured runner.  
- **scout-scan**: Run cve scan against all bake file defined targets using Docker Scout.  
- **sign-manifest**: Sign all images defined in bake file using cosign.  
- **tag-repository**: Apply YEAR.MO tag to a repo to trigger an automated monthly release cycle.  
- **tagx**: Add release tag to dev-build commit.  

Review gha-tools-workflows to see normal usage.  
