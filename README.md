## Helm Example with Skupper 1.X

### Problem Statement

Create a helm chart that deploys a hello world application within a skupper network, by using the skupper declarative configuration.

### Success Criteria

```
- App is deployed in Kubernetes along its respective services
- Services are exposed through skupper
- Two sites are created and linked
- There must be configurable bits, like the skupper version or the credentials for the console
```

### Solution Design
#### High-Level Architecture

The example will consist of:
- Frontend: Nginx and LoadBalancer service
- Backend: Backend google service
- Two sites
- Two site-controllers (one for each namespace)
- A token request
- A helm job to generate a token and create a link between the sites

#### Components

- Helm Job: token-job
    	Basically, this job replaces the interaction with the CLI needed to create the link. It generates a token in the public namespace and creates a link based on that token in the private namespace.

#### Implementation Plan

- The job is triggered after all the deployments contained in the templates folder (including the token request).
- A kubectl container is used to export the token from the public site. 
- That token will be applied to the private site to create a link. 

### Lessons Learned

- When uninstalling the helm chart, if namespaces are created with Helm, they will remain indefinitely in a “Terminating” status. This is because the correct finalizers were not specified. 
- A site controller could watch all the namespaces, but it should be deployed in one of the namespaces that have a site. If not, the site controller will fail trying to create a token declaratively because it does not find a skupper installation where the token-request is.


### Conclusion
The whole installation was made just by installing this helm chart, in one cluster. This example could be expandable to several clusters if we change the kubeconfig parameter when using kubectl.
