# Global Config

All keys from the [Common Config](common.md) are valid in addition to the keys documented here.

## deployTargetMap

Map of deployment targets.  Supported deployment target types are:

- `com.boxboat.jenkins.library.deployTarget.KubernetesDeployTarget`

```yaml
deployTargetMap:
  dev01: !!com.boxboat.jenkins.library.deployTarget.KubernetesDeployTarget
    # kubernetes context to use
    contextName: boxboat
    # credential ID with kube config
    credential: kubeconfig-dev 
```

## environmentMap

Map of environments.  Environments reference a deployment target.  This way, underlying deployment targets can be switched out.

```yaml
environmentMap:
  dev:
    # deployTargetMap key to reference
    deployTargetKey: dev01
```

## git

Stores the git configuration.

```yaml
git:
  # repository where build versions are written to
  buildVersionsUrl: git@github.com/boxboat/build-versions.git
  # SSH key credential for git service account
  credential: git
  # email that Jenkins commits as
  email: jenkins@boxboat.com
  # regex to convert a git remote to friendly path
  # first capture group is the friendly path
  remotePathRegex: github\.com/(.*)\.git$
  # string to convert a friendly path to a git remote
  # {{ path }} is replaced with the git path
  remoteUrlReplace: git@github.com/{{ path }}.git
  # string to display a Git branch URL
  # {{ path }} is replaced with the git path
  # {{ branch }} is replaced with the git branch
  branchUrlReplace: https://github.com/boxboat/{{ path }}/tree/{{ branch }}
  # string to display a Git commit URL
  # {{ path }} is replaced with the git path
  # {{ hash }} is replaced with the git commit hash
  commitUrlReplace: https://github.com/boxboat/{{ path }}/commit/{{ hash }}
```

## notifyTargetMap

Map of notification targets.  Supported notification target types are:

- `com.boxboat.jenkins.library.notify.SlackWebHookNotifyTarget`
  - For use with the [Slack Incoming Webhooks App](https://boxboat.slack.com/apps/A0F7XDUAZ-incoming-webhooks?next_id=0)
  - Jenkins Credential referenced in `credential` is a Secret Text credential with the full webhook URL
- `com.boxboat.jenkins.library.notify.SlackJenkinsAppNotifyTarget`
  - For use with the [Slack Jenkns CI App](https://boxboat.slack.com/apps/A0F7VRFKN-jenkins-ci?next_id=0)
  - Use `channel` to override channel
```yaml
notifyTargetMap:
  default: !!com.boxboat.jenkins.library.notify.SlackWebHookNotifyTarget
    credential: slack-webhook-url
  jenkins-success: !!com.boxboat.jenkins.library.notify.SlackJenkinsAppNotifyTarget
    channel: "#jenkins-success"
  jenkins-failure: !!com.boxboat.jenkins.library.notify.SlackJenkinsAppNotifyTarget
    channel: "#jenkins-failure"
```

## registryMap

Map of Docker registries.

```yaml
registryMap:
  default:
    scheme: https
    host: dtr.boxboat.com
    credential: registry
    # string to display a registry image URL
    # {{ path }} is replaced with the image path
    # {{ tag }} is replaced with the image tag
    imageUrlReplace: https://dtr.boxboat.com/repositories/{{ path }}/{{ tag }}/linux/amd64/layers
```

## vaultMap

Map of Hashicorp Vault endpoints.  Either (`roleIdCredential` and `secretIdCredential`) or (`tokenCredential`) are required.

```yaml
vaultMap:
  default:
    # vault KV version
    kvVersion: 1
    # secret text credential ID with roleId
    roleIdCredential: vault-role-id
    # secret text credential ID with secretId
    secretIdCredential: vault-secret-id
    # secret text credential ID 
    tokenCredential: vault-token
    # full URL to vault
    url: http://localhost:8200
```

## repo

Repository configurations that are applied globally to all repositories.

- `repo.common`: [Common Configuration](common.md)
- `repo.build`: [Build Configuration](build.md)
- `repo.promote`: [Promote Configuration](promote.md)
- `repo.deploy`: [Deploy Configuration](deploy.md)

```yaml
repo:
  common:
    vaultKey: default
  promote:
    promotionMap:
      prod:
        event: commit/master
        promoteToEvent: tag/release
  deploy:
    deploymentMap:
      dev:
        environmentKey: dev
        event: commit/master
        trigger: true
```
